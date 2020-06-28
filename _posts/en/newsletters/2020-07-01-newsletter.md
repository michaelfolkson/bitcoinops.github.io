---
title: 'Bitcoin Optech Newsletter #104'
permalink: /en/newsletters/2020/07/01/
name: 2020-07-01-newsletter
slug: 2020-07-01-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter FIXME

## Action items

*None this week.*

## News

FIXME

- **Discussion of HTLC mining incentives:** Itay Tsabary, Matan
  Yechieli, Ittay Eyal [posted][] to Lightning-Dev mailing list a paper
  they've written that claims rational miners, with the right software,
  will not mine a transaction that can be added to the next block if
  there also exists a conflicting transaction that pays a sufficiently
  higher feerate---even if that conflicting transaction has a time lock
  that only allows it to be added in a block further in the future.

    This affects Hash TimeLock Contracts (HTLCs) which can be redeemed
    immediately by one party (Alice) using a preimage or later by a
    second party (Bob) after a timeout.  If Bob sees Alice spend the
    HTLC by broadcasting the preimage, Bob can create a timelocked spend
    that's not valid until the future but which bribes miners with the
    offer of a higher feerate so that they won't mine Alice's spend.
    HTLCs are currently used in LN, atomic swaps, and several other
    contract protocols.

    The authors of the paper propose a solution they call Mutually
    Assured Destruction HTLCs (MAD-HTLCs).  These require Bob provide a
    bond that miners will be able to claim if FIXME:harding finish

- FIXME:harding Maybe DDPTs

## Recently transcribed talks and conversations

*[Bitcoin Transcripts][] is the home for transcripts of technical
Bitcoin presentations and discussions. In this monthly feature, we
highlight a selection of the transcripts from the previous month.*

**Magical Bitcoin**

Alekos Filini presented at LA BitDevs on Magical Bitcoin, an early yet promising set of tools and libraries for onchain wallet developers. He explained that currently coin selection logic and transaction signing logic are having to be rewritten multiple times across multiple projects. Magical Bitcoin aims to provide peer reviewed, modular and extensible components to address this. A longer term ambition would be to provide a de facto standard platform to build native wallets and integrate wallets into existing projects. Filini demoed the current functionality of Magical Bitcoin which includes a playground with a Policy to Miniscript compiler and some rudimentary visualizations. It is written in Rust and leverages the rust-miniscript library and the open source Esplora block explorer.  ([transcript](https://diyhpl.us/wiki/transcripts/la-bitdevs/2020-05-21-alekos-filini-magical-bitcoin/), [video](https://www.youtube.com/watch?v=QVhC2DOIl7I))

**Watchtowers and BOLT 13**

Sergi Delgado appeared on Potzblitz to discuss the latest state of watchtower development and a proposed BOLT for a watchtower protocol specification. He explored the various trade-offs when designing a watchtower and the interplay between privacy requirements, accessibility, storage and fees charged. Delgado is working on the watchtower implementation “The Eye of Satoshi” at Talaia Labs which is aiming to be compliant with BOLT 13. However, BOLT 13 is still in draft form and requires review and feedback from the LN community. Delgado also highlighted how watchtowers are becoming increasingly critical in multiple settings such as Bitcoin vault designs, statechains and atomic swaps in addition to the initial Lightning setting.  ([transcript](https://diyhpl.us/wiki/transcripts/lightning-hack-day/2020-05-24-sergi-delgado-watchtowers/), [video](https://www.youtube.com/watch?v=Vkq9CVxMclE), [slides](https://srgi.me/resources/slides/Potzblitz!2020-Watchtowers.pdf))

**CoinSwap**

Aviv Milner presented on CoinSwap at the Wasabi Research Club. He explained the property of covertness and how Chris Belcher’s CoinSwap proposal provides covertness in a manner that other privacy schemes such as Coinjoin fail to do. Milner also went through the motivation for routing CoinSwaps, namely to address the concern of unwittingly entering into a CoinSwap with an adversary such a chain surveillance company.  ([transcript](https://diyhpl.us/wiki/transcripts/wasabi-research-club/2020-06-15-coinswap/), [video](https://www.youtube.com/watch?v=Pqz7_Eqw9jM))

**Taproot and Schnorr Multisignatures**

Tim Ruffing presented at London Bitcoin Devs on multisignature and threshold signature schemes using Schnorr signatures. He highlighted how the rogue key attack and an attack using Wagner’s algorithm need to be addressed when designing secure multisignature schemes. Ruffing has been working with collaborators on a speculative MuSig signature scheme that only requires two rounds of interaction rather than three without relying on zero knowledge proofs. Threshold signature schemes generally present further challenges such as requiring an honest majority, a broadcast channel and have historically been designed without considering parallel signing sessions.

Ruffing’s presentation was preceded the previous day with a wide ranging discussion on BIP Schnorr (BIP 340) with some of the co-authors such as Ruffing, Pieter Wuille and Jonas Nick attending. This covered the history and evolution of BIP 340, some of the earlier ideas for implementing Schnorr in Bitcoin and what the co-authors thought the community should be concerned with when considering a possible future soft fork deployment. Wuille expressed his view that it is unlikely at this stage that there are bugs in the consensus implementation and that he is more concerned with how Schnorr is adopted and what is built using it than any underlying issues with the consensus implementation.

([Meetup transcript](https://diyhpl.us/wiki/transcripts/london-bitcoin-devs/2020-06-16-socratic-seminar-bip-schnorr/), [Presentation transcript](https://diyhpl.us/wiki/transcripts/london-bitcoin-devs/2020-06-17-tim-ruffing-schnorr-multisig/), [Meetup video](https://www.youtube.com/watch?v=uE3lLsf38O4), [Presentation video](https://www.youtube.com/watch?v=8Op0Glp9Eoo), [Presentation slides](https://slides.com/real-or-random/taproot-and-schnorr-multisig))

**Sydney meetup discussion**

A number of Bitcoin developers and LN developers joined this Sydney meetup to discuss various topics. Ruben Somsen gave a short presentation on Succinct Atomic Swaps (SAS). Somsen explained how Succinct Atomic Swaps compared to Chris Belcher’s CoinSwap proposal, whether atomic swaps via privacy focused altcoins is a workable approach to obtain additional privacy and how SAS only requires one of the two blockchains to have script functionality and timelocks.

Other non-related topics included reliance on DNS seeds in Bitcoin Core, whether they present a viable attack vector against new full nodes seeking to locate network peers for the very first time and resurfacing work by Matt Corallo and Antoine Riard on downloading block headers over HTTP and AltNet respectively, that could potentially mitigate this risk. Lloyd Fournier also discussed how experimenting with his toy Rust implementation of secp256k1 (secp256kfun) led to a small fix in the ECDSA signature code in the actual secp256k1 library. The transcript was anonymized to encourage open discussion.  ([transcript](https://diyhpl.us/wiki/transcripts/sydney-bitcoin-meetup/2020-06-23-socratic-seminar/))


## Releases and release candidates

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

FIXME

- [LND 0.10.2-beta.rc2][lnd 0.10.2-beta] this release candidate for an
  LND maintenance release is now available for testing.

FIXME:hwi
FIXME:btcpay

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo],
[C-Lightning][c-lightning repo], [Eclair][eclair repo], [LND][lnd repo],
[Rust-Lightning][rust-lightning repo], [libsecp256k1][libsecp256k1 repo],
[Bitcoin Improvement Proposals (BIPs)][bips repo], and [Lightning
BOLTs][bolts repo].*

FIXME: add HWI

- [Eclair #1466][] Increase fulfill safety window (#1466) FIXME:jnewbery

- [Bitcoin Core #19305][] doc: add C++17 release note for 0.21.0 FIXME:dongcarl

- [Bitcoin Core #11413][] updates the `bumpfee`, `fundrawtransaction`,
  `sendmany`, `sendtoaddress`, and `walletcreatefundedpsbt` RPCs to
  allow manually specifying the feerate to use in the newly created transaction.
  By default, these command still use either an automatic feerate
  computed by Bitcoin Core's built-in transaction fee estimate or the
  fallback feerate if there's not enough data for estimation.  For
  details on how to use the updated RPCs, see the [proposed release
  note][].  Despite not affecting any major system, this PR was open for
  almost three years---the second longest of any PR that has been merged
  so far in Bitcoin Core.  We thank the author, Kalle Alm, and everyone
  else involved for their persistance.

- [LND #4018][] adds the ability to delay forwarding a payment (HTLC),
  giving an external process the ability to review it and decide whether
  to cancel it or continue forwarding it.  This is similar to [hold
  invoices][] but it applies to payment being routed to a node's peers
  rather than received by the node itself.  Several use cases are
  described in the PR, such as being able to hold the HTLC, detect that
  its next hop if offline (e.g. a mobile node), send a notification
  to that node which will restart it, and then continue relaying the
  HTLC.

- [LND #4106][] adds a `getrecoveryinfo` RPC that returns information
  about the progress of restoring from a backup.

- [BTCPay Server #1639][] adds internal support for *pull
  payments*---payments the server is authorized to make to someone with
  a particular authorization token.  For example, Alice could send a URL
  to Bob that he can visit in order to enter his Bitcoin address and
  receive BTC deposited by Alice.  [BTCPay #1689][] builds on this
  feature to provide an interface that makes it easy to issue refunds.

- [BIPs #FIXME][] adds the [BIP339][] specification for transaction
  relay using Witness Transaction Identifiers (wtxids).   Currently,
  nodes announce new unconfirmed transactions to their peers using the
  transaction's txid, which is a hash over the legacy fields of the
  transaction.  The new fields used in segwit transactions are not
  included in the txid, which was neccesary to eliminate third-party and
  counterparty transaction malleability.  However, during the review of
  segwit, Peter Todd [noticed][] that relaying transactions by their
  txid could allow nodes to modify the segwit fields before relaying a
  transaction.  This couldn't be used to steal money directly, but it
  did allow a relay node to mutate a valid transaction into an invalid
  transction without changing its txid.

    Before Todd's discovery, this was a problem: Bitcoin Core would
    track the txids of invalid transactions it had recently seen and
    refuse to download them again.  That meant a mallicious node could
    prevent any of its peers from downloading a valid segwit transaction by
    sending them a mutated version of that transaction.  Transaction
    censorship, even just a the relay level, is bad by itself---but it
    has especially severe consequences for time-sensitive protocols such
    as LN.

    As segwit development was nearly complete at the time of Todd's
    discovery, a quick fix was implemented that Bitcoin Core will not
    cache the rejection status of segwit transactions that fail for
    the type of witness-related errors that mallicious nodes can inject.
    This eliminated the issue but it does mean that Bitcoin Core uses
    more bandwidth than necessary when it encounters a segwit
    transactions that are invalid because of accidental mistakes.  It
    may also complicate the development of new relay protocols such as
    [package relay][topic package relay].

    The long-term solution to the problem is that transactions should be
    relayed using a hash that commits to the entire transaction---both
    the legacy fields and the new segwit fields.  That's exactly what
    wtxids do already when they're used to construct the witness merkle
    root in each block's coinbase transaction.  This new BIP proposes
    updating the P2P protocol's `inv` message that announces new
    transactions and the `getdata` message that requests a transaction
    to allow using wtxids.  That will allow nodes to skip re-downloading
    a transaction if it has the same wtxid as a transaction that was
    previously found to be invalid.

- [BIPs #FIXME][] adds the [BIP78][] specification for the version of
  the [payjoin][] protocol originally implemented in BTCPay (see
  [Newsletter #FIXME][]).  Payjoin allows a spender and a receiver to
  both add UTXOs to a transaction, decreasing the reliability of the
  assumption made by those performing third-party block chain
  surveilance that any set of UTXOs spent in the same transaction were
  all received by the same person.  This documented is based on the
  [BIP79][] specification of the Bustapay payjoin variant (see
  [Newsletter #FIXME][] but contains several notable differences,
  including different extensions to [BIP21][] `bitcoin:` URIs, usage of
  PSBTs, and some additional requirements designed to enhance privacy.
  Several programs already support for this protocol and
  several more are currently working on adding support.

- [BIPs #FIXME][] and [#eda06164d3cc54d6b2e6e565417543e2249bec8f][]
  revise the [BIP8][] alternative to [BIP9][] versionbits-based soft
  fork deployment.  BIP8 previously allowed a soft fork to be activated
  by miner signaling using the same parameters as would be used for
  BIP9, but it required that the soft fork activate at the end of the
  signaling period even if miners were still not signaling readiness to
  follow the new consensus rules.  This could be used to override miners
  who were obstructing activation of a fork, but it could also lead to
  diverging block chains between nodes that enforced the fork's new
  consensus rules and those that didn't.

    The main changes to BIP8 from its previous version is that it
    may now be used initially without requiring mandatory
    lockin.  Implementations may choose to commit to lockin after their
    initial deployment of a BIP8-activated soft fork.  BIP8 mandates
    signaling for a period after the madatory lockin height which will
    trigger even deployments without mandatory locking to activate the
    rules at the same time as mandatory lockin nodes.

{% include references.md %}
{% include linkers/issues.md issues="" %}
[lnd 0.10.2-beta]: https://github.com/lightningnetwork/lnd/releases/tag/v0.10.2-beta.rc2
