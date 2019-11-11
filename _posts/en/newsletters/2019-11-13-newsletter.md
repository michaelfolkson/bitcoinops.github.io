---
title: 'Bitcoin Optech Newsletter #72'
permalink: /en/newsletters/2019/11/13/
name: 2019-11-13-newsletter
slug: 2019-11-13-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter announces the latest major release of Bitcoin
Core and links to a security disclosure affecting some older releases,
describes new developments related to taproot, mentions a potential
privacy leak related to the LN payment data format, and describes two
proposed changes to the LN specification that are under discussion.
Also included is our regular section describing notable changes to
popular Bitcoin infrastructure projects.

{% comment %}<!-- include references.md below the fold but above any Jekyll/Liquid variables-->{% endcomment %}
{% include references.md %}

## Action items

- **Upgrade to Bitcoin Core 0.19.0:** users are encouraged to upgrade to the
  latest [release][bitcoin core 0.19.0], which contains new features and
  multiple bug fixes.

## News

- **CVE-2017-18350 SOCKS proxy vulnerability:** a full disclosure of a
  vulnerability in Bitcoin Core versions 0.11.0 (Sept 2012) to 0.15.1
  (Nov 2017) was [published][cve-2017-18350] to the Bitcoin-Dev mailing
  list.  The vulnerability only affects users who have configured a
  SOCKS proxy to use either an untrusted server or to connect over an
  untrusted network.  Almost all affected versions of Bitcoin Core are
  also affected by previously disclosed vulnerabilities, such as
  CVE-2016-10724 (see [Newsletter #1][news1 alert]) and CVE-2018-17144
  (see [Newsletter #14][news14 cve]), so users should have already
  upgraded to Bitcoin Core 0.16.3 or higher.

{% comment %}
<!-- commit count: git shortlog -no-merges -s v0.18.1..v0.19.0 | awk '{print $1}' | numsum
     contributor count: wc -l on list in release notes -->
{% endcomment %}

- **Bitcoin Core 0.19.0 released:** featuring over 1,000 commits from
  more than 100 contributors, the
  [latest Bitcoin Core release][bitcoin core 0.19.0] offers several new
  user-visible features, numerous bug fixes, and multiple improvements
  to internal systems such as P2P network handling.  Some changes which
  may be especially interesting to newsletter readers include:

    - *CPFP carve-out:* this new mempool policy helps two-party contract
      protocols (such as the current LN) ensure that both parties will
      be able to use Child-Pays-For-Parent (CPFP) fee bumping (see
      [Newsletter #63][news63 carve-out]).  LN developers already have a
      proposal under discussion for how they'll use this feature to
      simplify fee management for commitment transactions (see
      newsletters [#70][news70 simple commits] and [#71][news71 ln carve-out]).

    - *BIP158 block filters (RPC only):* users can now set a new
      configuration option, `blockfilterindex`, if they want Bitcoin Core
      to generate [BIP158][] compact block filters.  The filter for each
      block can then be retrieved using the new RPC `getblockfilter`.
      Filters can be provided to a compatible lightweight client to
      allow it to determine whether a block might contain any
      transactions involving its keys (see [Newsletter #43][news43
      core bip158] for more information).  [PR#16442][Bitcoin Core
      #16442] is currently open to add support for the corresponding
      [BIP157][] protocol that will allow sharing these filters with
      clients over the P2P network.

    - **Deprecated or removed features:** support for the [BIP70][]
      payment protocol, [BIP37][] P2P protocol bloom filters, and
      [BIP61][] P2P protocol reject messages has been disabled by
      default, eliminating the source of various problems (respectively,
      see Newsletters [#19][news19 bip70], [#57][news57 bip37], and
      [#37][news37 bip61]).  The payment protocol and reject messages
      are scheduled to be fully removed in the next major Bitcoin Core
      version about six months from now.

    - **Customizable permissions for whitelisted peers:** when
      specifying which peers or interfaces should be whitelisted, users
      can now specify which special features the whitelisted peers
      can access.  Previously, whitelisted peers wouldn't be banned and
      received relayed transactions faster.  These defaults haven't
      changed, but now it's possible to toggle those settings on a
      per-peer basis or to allow specified whitelisted peers to request
      BIP37 bloom filters even though they're disabled for
      non-whitelisted peers by default.  For details, see [Newsletter
      #60][].

    - **GUI improvements:** graphical users can now create new wallets
      for use with multiwallet mode from the GUI's *file* menu (see
      [Newsletter #63][news63 new wallet]).  The GUI also now provides
      users with bech32 Bitcoin addresses by default, but users can
      easily request a backwards-compatible P2SH-P2WPKH address by
      toggling a checkbox next to the button to generate an address (see [Newsletter #42][news42 core gui
      bech32]).

    - **Optional privacy-preserving address management:** a new
      `avoid_reuse` wallet flag, which can be toggled using a new
      `setwalletflag` RPC, can be enabled to prevent the wallet from
      spending bitcoins received to an address that was previously
      used (see [Newsletter #52][news52 avoid_reuse]).  This prevents
      certain privacy leaks based on block chain analysis such as [dust
      flooding][].

    For a full list of notable changes, links to the PRs where those
    changes were made, and additional information useful to node
    operators, please see the Bitcoin Core project's [release notes][notes
    0.19.0].

    Note that, as of this writing, both released versions and the
    master development branch of LND are not compatible with Bitcoin
    Core 0.19.0.  See [LND #3649][] for more information and for the
    status of whether LND has been upgraded to address the
    incompatibilities.

- **Taproot review, discussion, and related information:** 163 people
  signed up for the structured taproot review mentioned in [Newsletter
  #69][news69 taproot review].  Last week they [began reviewing][tr week
  1] the first part of [bip-taproot][] and related concepts, including
  participating in question and answer sessions with Bitcoin experts who
  previously contributed to the taproot design.

    One [question][why v1 flex] asked was about why taproot allows v1
    segwit scriptPubKeys to use fewer or more than 34 bytes---the amount
    needed for a Pay-to-Taproot (P2TR) scriptPubKey.  This seems odd
    since [BIP141][] v0 segwit scriptPubKeys are only allowed to use
    the exactly 22 or 34 bytes needed for P2WPKH and P2WSH.  The
    response was that fewer restrictions will allow a later soft fork to
    define other uses for v1 scriptPubKeys with shorter or longer
    lengths.  In the meantime, if taproot is adopted, those shorter and
    longer v1 scriptPubKeys will be spendable by anyone (as they are
    today).

    This started a discussion among experts about a recently discovered
    issue related to how this flexibility interacts with a somewhat
    older [issue][bech32 length change] discovered in the bech32 address
    encoding algorithm.  [BIP173][] bech32 addresses are supposed to
    guarantee that up to four errors will be detected in an incorrectly
    copied address and that only about one miscopied address in a
    billion that had five or more errors would go undetected.
    Unfortunately, those calculations were made under the assumption
    that the length of the copied address would be the same as the
    original.  If the copied address is instead longer or shorter,
    bech32 can fail to detect even a single-character error on rare
    occasions.

    For existing P2WPKH and P2WSH bech32 addresses, this is very
    unlikely to be an issue since the restriction that v0
    scriptPubKeys be exactly 22 or 34 bytes means that a miscopied
    P2WPKH address would need to either contain an extra 12 bytes or a
    P2WSH address would need to omit 12 bytes, meaning a user would need
    to type about 19 extra or fewer bech32 characters---an awfully big
    mistake.  <!-- 8 bits per byte * 12 bytes / 5 bits per bech32
    character = 19.2 bech32 characters -->

    But if P2TR is only defined for 34-byte v1 scriptPubKeys and it
    remains the case that anyone can spend 33-byte and 35-byte v1
    scriptPubKeys, then it would be possible for a user to make a
    single-character mistake and lose all of the money they intended to
    spend.  Author of both BIP173 and the taproot proposal Pieter Wuille
    [posted][wuille bech32 workaround] to the Bitcoin-Dev mailing some
    options for addressing the problem and requested feedback on what
    options people would prefer to see implemented.  One option would be
    restricting all current bech32 implementations to rejecting any
    native segwit addresses that don't result in a 22 or 34 byte
    scriptPubKey.  Then an upgraded version of bech32 could later be
    developed with better detection of inserted or deleted characters.

    Many other less critical discussions also resulted from the week's
    review of taproot, and discussion logs are available ([1][tr meet1],
    [2][tr meet2]) for anyone interested in the discussion details.

    {:#x-only-pubkeys}
    In other schnorr/taproot news, Jonas Nick published an [informative
    blog post][x-only pubkeys] about a recent major change to
    [bip-schnorr][] and [bip-taproot][] that reduced the size of
    serialized public keys from 33 bytes to 32 bytes without reducing
    security. See Newsletters [#59][news59 proposed 32B pubkeys] and
    [#68][news68 taproot update] for previous discussion of this
    optimization.

- **Possible privacy leak in the LN onion format:** as described in
  [BOLT4][], LN uses the [Sphinx][] protocol to communicate payment
  information between LN nodes.  Olaoluwa Osuntokun [posted][osuntokun
  sphinx] to the Lightning-Dev mailing list this week about a
  [recently-published][breaking onion routing] flaw in the original
  description of Sphinx that may allow a destination node to "deduce a
  lower bound for the length of the path [back to the source node]."
  <!-- quote from Osuntokun email -->  The fix is easy: instead of
  initializing part of an onion packet with zero bytes, random-value
  bytes are used instead.  Osuntokun created a [PR][lnd-onion]
  implementing this in the onion library used by LND as well as a
  [documentation PR][bolts #697] for the BOLTs repository.  Other
  implementations have also adopted the same change (see the
  [C-Lightning commit][news72 cl onion] below).

- **LN up-front payments:** the current LN protocol returns all money to
  the spender if a payment attempt fails or is rejected by the receiver,
  so routing nodes only receive income if the payment attempt succeeds.
  However, some new applications are using this costless failure
  mechanism to send data over LN without paying for the bandwidth they
  use.  LN designers expected this to happen and have previously spent
  time thinking about how they could add up-front fees to the
  network---fees that would be paid to routing nodes whether or not a
  payment attempt succeeded.

    This week, Rusty Russell started a [thread][russell up-front] on the
    Lightning-Dev mailing list to discuss proposals for up-front fees.
    Russell proposed a mechanism that combines fees and hashcash-style
    proof-of-work to try to prevent nodes from using using the extra
    up-front payment information to guess the length of the route.
    Anthony Towns proposed a partial [alternative][towns up-front]
    focusing on managing payment amounts using a refund mechanism.

    Joost Jager suggested that up-front payments should only be required
    as a last resort because even small additional fees could make
    micropayments uneconomic.  He suggested that it should be possible
    to address bandwidth-wasting network activity using rate limits
    based on node reputation and, further, that research into up-front
    payments should focus on first solving liquidity abuse---where an
    attacker ties up someone's in-channel funds for period of time---as
    the solution to that problem may also prevent the abuse of routing
    node bandwidth.

    Ultimately, no conclusions were reached and discussion about the
    topic remains ongoing as of this writing.

- **Proposed BOLT for LN offers:** Rusty Russell [posted][bolt offers]
  draft text for a new BOLT that would allow users to submit offers and
  receive invoices over the LN routing protocol.  For example, Alice
  could subscribe to a service provided by Bob where each month she
  would submit an offer to pay Bob, Bob would reply with an invoice,
  Alice would pay the invoice, and Bob would provide the service.

    Early feedback on the proposal suggested that it might want to
    incorporate by reference an established language for
    machine-readable invoices, such as the [Universal Business
    Language][] (UBL).  However, there was a concern that implementing
    the full UBL specification would be an excessive burden on developers
    of LN software.

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo],
[C-Lightning][c-lightning repo], [Eclair][eclair repo], [LND][lnd repo],
[libsecp256k1][libsecp256k1 repo], [Bitcoin Improvement Proposals
(BIPs)][bips repo], and [Lightning BOLTs][bolts repo].*

- [Bitcoin Core #16110][] adds support for building Bitcoin Core (including
  the GUI) for the Android Native Development Kit (NDK).  In contrast to
  independent projects like [Android Bitcoin Core][] (ABcore) that build
  their own Bitcoin Core binaries for Android NDK, adding support
  directly to the Bitcoin Core project may simplify testing and could
  allow future PRs to add the Android builds to the set of
  deterministically-generated executables that allow users to receive
  greater assurances that they are running the same well-reviewed code
  that exists in the code repository.

- [Bitcoin Core #16899][] adds a `dumptxoutset` RPC that will write a
  copy of the current UTXO set to disk in a serialization format
  designed for future use with nodes bootstrapped using assumeutxo (see
  [Newsletter #41][news41 assumeutxo]).  Additionally, a script is added to the
  project's contributor tools that will rewind the block chain to a
  specified block height, dump the UTXO set at that point, and then
  start reprocessing blocks normally again.  This can take several
  seconds per block, so running this script for a height thousands of
  blocks in the past can take a very long time.

- [Bitcoin Core #17258][] updates the `listsinceblock` RPC to prevent
  listing any transactions that can't be confirmed because another
  transaction spent at least one of the same inputs and was already
  included in the block chain prior to the blocks being evaluated by the
  RPC call.

- [C-Lightning #3246][] attempts to fix the potential data leak
  described on the LN mailing list this week (see the [news item][news72
  sphinx]).

- [LND #3442][] makes it possible for a spender to manually construct
  packets for simple multipath payments---payments that are split into
  parts and which can be independently routed through different
  channels.  This isn't meant to be accessed by users but rather to be
  built upon by subsequent features that add other functions related to
  multipath payments.  For more about multipath payments, see
  [Newsletter #27][news27 multipath].

- [BIPs #857][] edits [BIP157][] to increase the maximum number of
  compact block filters that can be requested at once from 100 to 1,000.
  This reverts a [PR from last year][BIPs #699] that lowered it from
  1,000 to 100.

- [BIPs #849][] edits [BIP174][] to allocate certain data type
  identifiers in Partially Signed Bitcoin Transactions (PSBTs) for use
  by non-standardized (proprietary) applications.  Additionally, PSBTs
  are now given a version number to help identify backwards-incompatible
  changes to PSBTs, with older PSBTs that don't contain an explicit
  version number having an implicit version number of 0.  Both changes
  were previously discussed on the Bitcoin-Dev mailing list (see
  [Newsletter #58][news58 psbts]).

- [BIPs #856][] adds [BIP179][], a proposal that suggests renaming the
  current term "Bitcoin address" to "Bitcoin invoice address" or simpler
  variations such as "invoice address" or just "invoice".  This was
  previously [discussed][bip179 genesis] on the Bitcoin-Dev mailing list.

- [BIPs #803][] adds [BIP325][] with a description of the signet protocol
  for creating testnets based on signed blocks instead of mined blocks,
  allowing the operators of the signet to control the rate of block
  production and the frequency and magnitude of block chain
  reorganization (see [Newsletter #37][news37 signet]).

- [BIPs #851][] adds [BIP330][] with a description of the transaction
  announcements reconciliation method intended to be used as part of the
  [erlay][] protocol (see [Newsletter #66][news66 erlay]).  If this
  feature is adopted by node software, it will be the first step in
  significantly reducing the bandwidth overhead of relaying transaction
  announcements, which may consume over 40% of a typical node's
  bandwidth at present.

{% include linkers/issues.md issues="16110,16899,17258,3246,3442,857,856,803,851,16442,3649,697,849,699" %}
{% assign bse = "https://bitcoin.stackexchange.com/a/" %}
[bitcoin core 0.19.0]: https://bitcoincore.org/bin/bitcoin-core-0.19.0/
[x-only pubkeys]: https://medium.com/blockstream/reducing-bitcoin-transaction-sizes-with-x-only-pubkeys-f86476af05d7
[news1 alert]: /en/newsletters/2018/06/26/#pending-dos-vulnerability-disclosure-for-bitcoin-core-0-12-0-and-earlier-altcoins-may-be-affected
[news63 carve-out]: /en/newsletters/2019/09/11/#bitcoin-core-16421
[news70 simple commits]: /en/newsletters/2019/10/30/#ln-simplified-commitments
[news14 cve]: /en/newsletters/2018/09/25/#cve-2018-17144
[news71 ln carve-out]: /en/newsletters/2019/11/06/#continued-discussion-of-ln-anchor-outputs
[news43 core bip158]: /en/newsletters/2019/04/23/#basic-bip158-support-merged-in-bitcoin-core
[news19 bip70]: /en/newsletters/2018/10/30/#bitcoin-core-14451
[news57 bip37]: /en/newsletters/2019/07/31/#bloom-filter-discussion
[news37 bip61]: /en/newsletters/2019/03/12/#removal-of-bip61-p2p-reject-messages
[news63 new wallet]: /en/newsletters/2019/09/11/#bitcoin-core-15450
[news42 core gui bech32]: /en/newsletters/2019/04/16/#bitcoin-core-15711
[news69 taproot review]: /en/newsletters/2019/10/23/#taproot-review
[sphinx]: https://cypherpunks.ca/~iang/pubs/Sphinx_Oakland09.pdf
[news41 assumeutxo]: /en/newsletters/2019/04/09/
[news27 multipath]: /en/newsletters/2018/12/28/#multipath-payments
[news58 psbts]: /en/newsletters/2019/08/07/#bip174-extensibility
[news37 signet]: /en/newsletters/2019/03/12/#feedback-requested-on-signet
[news66 erlay]: /en/newsletters/2019/10/02/#draft-bip-for-enabling-erlay-compatibility
[cve-2017-18350]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-November/017453.html
[notes 0.19.0]: https://FIXME/not/uploaded/yet
[breaking onion routing]: https://arxiv.org/abs/1910.13772
[bech32 length change]: https://github.com/sipa/bech32/issues/51
[lnd-onion]: https://github.com/lightningnetwork/lightning-onion/pull/40
[news72 cl onion]: #c-lightning-3246
[tr week 1]: https://github.com/ajtowns/taproot-review/blob/master/week-1.md
[why v1 flex]: http://www.erisian.com.au/meetbot/taproot-bip-review/2019/taproot-bip-review.2019-11-05-19.00.log.html#l-88
[wuille bech32 workaround]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-November/017443.html
[tr meet1]: http://www.erisian.com.au/meetbot/taproot-bip-review/2019/taproot-bip-review.2019-11-05-19.00.log.html
[tr meet2]: http://www.erisian.com.au/meetbot/taproot-bip-review/2019/taproot-bip-review.2019-11-07-02.00.log.html
[russell up-front]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2019-November/002275.html]
[towns up-front]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2019-November/002307.html
[bolt offers]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2019-November/002276.html
[osuntokun sphinx]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2019-November/002288.html
[universal business language]: https://en.wikipedia.org/wiki/Universal_Business_Language
[android bitcoin core]: https://github.com/greenaddress/abcore
[news72 sphinx]: #possible-privacy-leak-in-the-ln-onion-format
[bip179 genesis]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-October/017369.html
[news52 avoid_reuse]: /en/newsletters/2019/06/26/#bitcoin-core-13756
[dust flooding]: {{bse}}81509
[news59 proposed 32B pubkeys]: /en/newsletters/2019/08/14/#proposed-change-to-schnorr-pubkeys
[news68 taproot update]: /en/newsletters/2019/10/16/#taproot-update
