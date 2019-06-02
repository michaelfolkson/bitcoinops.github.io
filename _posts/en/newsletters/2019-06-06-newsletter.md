---
title: 'Bitcoin Optech Newsletter #49'
permalink: /en/newsletters/2019/06/05/
name: 2019-06-05-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter FIXME

{% comment %}<!-- include references.md below the fold but above any Jekyll/Liquid variables-->{% endcomment %}
{% include references.md %}

## Action items

FIXME

## News

- FIXME erlay, rust binidngs
- FIXME TODO exec briefing, LN by Sergej Kotliar

- **COSHV proposal replaced:** the author of the COSHV proposal we described
  [last week][news48 coshv] has replaced it with a [similar
  proposal][alt-coshv] (under a different name).  The new proposal checks more
  than just the hash of a transaction's outputs, now it includes the transaction's
  version number, number of inputs, sequence numbers, and locktime.  This
  change eliminates concerns related to transaction malleability that would've
  affected using the opcode with some types of payment channels (such as those
  used by LN) and possibly other contract protocols.  Additionally, by hashing the
  number of inputs allowed in the transaction, the original proposal's
  restriction on just one input is lifted (however, the proposal notes that only
  allowing a single input is still recommended to avoid unwanted interactions).

    Except for the new name, the changes don't affect the summary of COSHV we
    wrote last week.  However, the changes do affect some additional material about
    COSHV use cases that we planned to publish this week, so we're not going to
    publish that information.  Instead, we'll focus on keeping you advised about
    future developments related to the COSHV idea.

- FIXME check for other news

## Bech32 sending support

*Week 12 of 24 in a [series][bech32 easy] about allowing the people
you pay to access all of segwit's benefits.*

{% comment %}<!-- weekly reminder for harding: check Bech32 Adoption
wiki page for changes -->{% endcomment %}

{% include specials/bech32/12-midway.md %}

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo],
[LND][lnd repo], [C-Lightning][c-lightning repo], [Eclair][eclair repo],
[libsecp256k1][libsecp256k1 repo], and [Bitcoin Improvement Proposals
(BIPs)][bips repo].*

- [Bitcoin Core #15741][] speeds up importing keys, addresses, and other
  information into the wallet using the `importmulti` RPC by batching the
  database updates rather than making them sequentially.  In a test performed by
  the PR author, this reduced the time to import 10,000 addresses from 465
  seconds to 4 seconds.

- [LND #2985][] waits to relay gossip announcements until there are there are
  at least ten of them to send and five seconds have elapsed since the previous
  batch, reducing bandwidth overhead.

- [LND #2761][] switches to using a persistent state machine for routed
  payments and to storing some additional state data for the machine, improving
  the program's ability to correctly handle HTLCs that were unresolved when the
  daemon was restarted.

{% include linkers/issues.md issues="15741,2985,2761" %}
[bech32 easy]: {{news38}}#bech32-sending-support
[news48 coshv]: {{news48}}#proposed-transaction-output-commitments
[alt-coshv]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-June/016997.html
