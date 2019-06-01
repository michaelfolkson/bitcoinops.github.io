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

### More advanced usage

In our previous examples, getting the distribution transaction confirmed
was an all-or-nothing endeavor.  Either all ten receivers finally
received their money at the same time, or all of them were still waiting
for that confirmation to occur.  However, with Taproot and COSHV,
it's possible to allow each receiver to individually withdraw their
money from the covenant while also allowing most the other receivers to
keep their money offchain for the moment.

For example, let's imagine Alice paid each of our ten receivers 1 BTC
using a tree of outputs where each output paid two or three other outputs until
an individual's money was withdrawn from the covenant.  A complete tree
might for ten participants might look like this:

![Example output tree for ten participants](/img/posts/2019-05-tx-tree-5.png)
{:.center}

If receiver Bob wants to individually withdraw now, he could
follow the whole path until he received his output, allowing him to
create only five outputs instead of the ten he'd need to create if
everyone had to exit at the same time.

![Example output tree to allow participant Bob to exit](/img/posts/2019-05-tx-tree-6.png)
{:.center}

Since Bob is the first participant to withdraw, he has to create more
outputs than everyone else.
Anyone else exiting after Bob would need to create even fewer outputs as
they could just start spending from one of the intermediate COSHV
outputs that Bob had caused to be confirmed onchain.  Some people (like Charlie in the above
example) would get their exit outputs as a byproduct of another person's
exit.  Overall, the total number of outputs that need to be created
would be greater than the number of participants.  However, if nobody
tried to exit early, a Taproot leaf at the top level could optionally
allow everyone to exit together using a normal batched payment, which is
just one output per receiver.

Even with the simple example above we again see the key benefit
of COSHV is its ability to allow Alice to construct this complex
multi-transaction tree without any interaction from the receivers after
she has each participant's own desired withdrawal address.  It's
possible to construct the same tree using just the pre-signed
transactions we saw in the first sub-section above, but each of the
possible output sets would be a separate transaction that would need to
be interactively signed by all participants before the setup transaction
could be broadcast.  For larger numbers of participants, this could
easily require the signing of many thousands of transactions by each
participant.

### Rolling coinjoin ("joinpool")

Gregory Maxwell [discussed][gmaxwell joinpool] some of his first
impressions of the proposal to the logged IRC channel #bitcoin-wizards.
Among them was a description of how the above multi-transaction tree
could be used as part of a coinjoin construction to manage
balances offline.

The coinjoin would start with a set of users creating a setup
transaction.  This would be similar to the examples above but it would
require interaction because, before each participant could sign the
setup transaction, they'd need to verify that the outputs tree paid them
the correct amounts.  After the setup transaction was broadcast, they
could then individually withdraw their balances the same as before, with
the same possible advantages of reduced fees if they were patient.

However, a more powerful feature would be to use Taproot key-path spends
to use the pool of funds to create transactions that
look like they were created by a single ordinary user.  This is possible
because a key-path spend using signature aggregation allows all members
of the pool, when they are in agreement, to spend their money however
they'd like.  For example:

1. If Bob wanted to spend part of his money to someone else (either
   someone in the pool or anyone outside of it), every member of the
   pool could sign that spend and return the change to the same basic
   output tree they already had, but with Bob's balance decreased
   accordingly.

2. If Fran wanted to join the pool by contributing some funds, Fran and
   every current member of the pool could sign a transaction spending
   their current UTXO along with Fran's UTXO or UTXOs to a new output tree
   that included Fran's withdrawal address and balance.  This same
   method could be used if any member of the pool wanted to pay into the
   pool in order to increase their balance there.

In this way, many transactions made by the pool would be key-path spends
that look identical to single-user, single-sig transactions and so would
not directly reveal that they belonged to the pool.  Even if it was
known that a joinpool controlled those funds, third parties would not
know who within the group had spent or received money.  Exit
transactions might reveal that someone was leaving a joinpool but, if
the group was involved in enough spending and receiving, it could be
very difficult for a third party to use the transaction history graph to
determine which member of the pool was exiting.

### Channel factories

Coinjoin isn't the only use case that may benefit from the proposal.  In
the previously-proposed LN [channel factories][], several participants
create an unsigned setup transaction merging their funds together in a
multisig; they then sign a distribution transaction containing a series of
outputs spending those funds with each participant opening a channel
with every other participant in the factory.  The distribution
transaction is not broadcast unless one of the participants needs to
unilaterally close a channel.

When the setup transaction is suitably confirmed, the participants can
safely use those LN channels like any other channel even though none of
those outputs from the distribution transaction have been actually
confirmed onchain---the security comes from the setup transaction being
confirmed and the distribution transaction being unalterable except by
complete agreement among the factory participants.  You may recall that
this is very similar to the scenario we described in the first part of
this COSHV section when we covered current onchain behavior.

However, one problem with factories is that anyone attempting to exit
the factory either needs the cooperation of all the other participants
or needs to broadcast the potentially large distribution transaction (e.g.
for a factory with 150 participants, the distribution transaction may have
over 11,000 outputs <!-- almost (n**2)/2 --> and be almost 50,000 vbytes
in size.  Since the other participants will probably want to stay in the
factory, it's likely the person exiting will be responsible for all
transaction fees, not just the fees for their 149 channels (outputs).

However, using a multi-transaction tree construction such as above, it's
possible for any participant to close any individual one of the their
channels onchain (or all of them at the same time, if the outputs tree
includes branches for those outcomes).  Also, like the joinpool, it's
possible for any participant to kick out any other participant (keeping
all channels intact), allowing active participants to remove inactive
participants when they want to rebalance the channel using group
multisig---saving block chain space and allowing the rebalancing details
to stay private from block chain analysis.

### Ongoing research

The author of the proposal also [discussed][wizards coshv optimization]
a potential optimization that may reduce some of the overhead of the
transactions by getting the hash of the outputs from the structure of
the Taproot MAST tree.  This could be a useful improvement for other
types of scripts as well.  We'll keep track of this developing idea and,
if it matures into a proposal, cover it in more detail in a future
newsletter.


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
[gmaxwell joinpool]: http://gnusha.org/bitcoin-wizards/2019-05-21.log
[wizards coshv optimization]: http://gnusha.org/bitcoin-wizards/2019-05-22.log
[channel factories]: https://www.tik.ee.ethz.ch/file/a20a865ce40d40c8f942cf206a7cba96/Scalable_Funding_Of_Blockchain_Micropayment_Networks%20(1).pdf
