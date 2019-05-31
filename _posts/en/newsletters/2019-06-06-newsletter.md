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

- FIXME erlay
- FIXME TODO exec briefing, LN by Sergej Kotliar
- FIXME check for other news

## FIXME: more COSHV

FIXME TODO

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

## Special thanks

We repeat last week's thanks to Jeremy Rubin and Anthony Towns for their
reviews of a draft of this newsletter's COSHV section.  Any errors in the
published version of this newsletter are the fault of the author.

{% include linkers/issues.md issues="15741,2985,2761" %}
[bech32 easy]: {{news38}}#bech32-sending-support
