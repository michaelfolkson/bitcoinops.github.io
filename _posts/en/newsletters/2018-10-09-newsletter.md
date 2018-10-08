---
title: 'Bitcoin Optech Newsletter #16: Scaling Bitcoin Special'
permalink: /en/newsletters/2018/10/09/
name: 2018-10-09-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter consists entirely of summaries of several notable
talks presented at the Scaling Bitcoin workshop last weekend, since
there was very little to report in our usual Action Items, News, and
Notable Code Changes sections. We hope to return to our usual format
next week.

## Workshop summary: Scaling Bitcoin V (Tokyo 2018)

The fifth Scaling Bitcoin conference was held Saturday and Sunday in
Tokyo, Japan.  In the sections below, we provide brief overviews to some
of the talks we think might be most interesting to this newsletter's
readers, but we also recommend watching the complete set of [videos][]
provided by the workshop organizers or reading the [transcripts][]
provided by Bryan Bishop.

For convenience, at the end of each summary we directly link to its
video and transcript (and paper, if available).  Talks are listed below
in the order they appeared in the workshop schedule.

**Warning:** the following summaries may contain errors due to many of
the talks describing subjects well beyond the expertise of the summary
author.

### Adjusting Bitcoin's block subsidy

*Research by Anthony (AJ) Towns*

This talk asks whether Bitcoin pays more for security than it needs to
and then considers options for reducing the amount of subsidy paid in
the short term as the amount of security increases---still ensuring that
no more than 21 million bitcoins are paid overall in subsidy, but
potentially allowing the subsidy to last much longer and also
potentially reducing the amount of extraneous security (if it exists).

Although the talk was not about a specific proposal, an example proposal
it evaluated was to reduce the subsidy by 20% every time the network's
proof of work security doubles (measured by block-creation difficulty).

*[video][vid subsidy], [transcript][tx subsidy]*

### Forward blocks: on-chain capacity increases without a hard fork

*Research by Mark Friedenbach*

One well-known method for soft forking an increase in the Bitcoin block
size is extension blocks---a data structure that's invisible to nodes
that haven't upgraded to the soft fork and so is not subject to their
historic limits on block size.  By itself, this is an undesirable method
for increasing block size because preventing old nodes from seeing the
transactions in the extension block also prevents them from being able
to enforce any other consensus rules on those transactions---such as
rules that prevent a malicious user from spending other users' bitcoins
or from creating more bitcoins than allowed by the 21 million bitcoin
subsidy schedule.

However, one doesn't need to increase block size to increase the amount
of data that can be added to the block chain per minute---it's also
possible to increase capacity by increasing the frequency of blocks
(reducing the average time between blocks).  A method for gaming
Bitcoin's difficulty adjustment algorithm---called a time-warp
attack---is well-known among experts and has been used successfully in
demonstration attacks against Bitcoin's testnet and real attacks against
altcoins.  (Note: although Bitcoin is technically vulnerable to this
attack, it'd be a slow attack that would give the userbase a significant
amount of time to respond.)  By itself, increasing block frequency is
also an undesirable method for increasing capacity because shorter block
intervals increase the effectiveness of miners with large amounts of
hashrate and so is likely to increase mining centralization.[^freq-pow-waste]

Perhaps disproving the saying that "two wrongs don't make a right," this
talk describes a novel way of combining extension blocks and the
time-warp attack to allow both upgraded nodes and old nodes to gain the
same capacity increase and see all the same transactions for validation
while simultaneously slightly reducing mining centralization risk.
Upgraded nodes would validate one or more extension blocks providing
additional block space with a 15 minute average interval, but they'd
also restrict the time stamps in legacy blocks to ensure a permanent
(but limited) time warp attack increased the frequency of legacy blocks
enough to allow them to include the same transactions that previously
appeared in the extension blocks.

*[video][vid forward blocks], [transcript][tx forward blocks],
[paper][paper forward blocks]*

### Compact multi-signatures for smaller blockchains

*Research by Dan Boneh, Manu Drijvers and Gregory Neven*

This talk describes an alternative to the Bellare-Neven-based Schnorr
signature scheme described in the [MuSig paper][] and [proposed as a
standard][schnorr pre-bip] for Bitcoin by Pieter Wuille.  The
alternative method makes use of [pairing-based cryptography][],
specifically an adaptation of the [Boneh-Lynn-Shacham (BLS) signature
scheme][bls sigs].  Although pairing-based schemes require an additional
fundamental security assumption beyond those made by both Bitcoin's
current ECDSA scheme and proposed Schnorr scheme, the authors present
evidence that their scheme would produce smaller signatures in general,
be much more computationally efficient in some multisignature use cases,
and make it possible to prove which members of the set of signers
actually worked together to create a threshold signature (i.e.  k-of-m
signers, e.g. 2-of-3 multisig).

*[video][vid bls msig], [transcript][tx bls msig], [paper
(pre-print)][paper bls msig]*

### Accumulators: a scalable drop-in replacement for merkle trees

*Research by Benedikt Bünz, Benjamin Fisch and Dan Boneh*

In Bitcoin and other cryptocurrencies, scalable commitments to sets of
information---such as transactions or UTXOs---are normally made using
merkle trees that allow proving an element is a member of the set by
generating a proof whose size and validation cost is roughly *log2(n)*
for a set of *n* elements.

This talk describes an alternative method based on RSA accumulators that
provides many potential benefits: the size of a proof is constant no
matter how many elements are members of the set, adding or removing
elements from an accumulator can be efficiently batched (e.g. one update
per block), and validation that one or more elements are members of a
set could potentially be much faster (although this has not yet been
fully tested).

*[video][vid accumulators], [transcript][tx accumulators]*

### Scriptless ECDSA

*Research by Conner Fromknecht*

At Scaling Bitcoin IV (Stanford 2017), Andrew Poelstra
[presented][scriptless scripts transcript] his existing "scriptless
scripts" work that suggests several ways outputs can be encumbered in
similar ways to a few of the things now possible with Bitcoin Script but
by using only Schnorr-based keys and signatures.  This method has the
advantage of being just as secure as the Script-based mechanism but also
maximally compact, faster to validate, and completely invisible to third
parties (providing privacy, increased fungibility, and potentially
increased security from the fungibility).

Bitcoin doesn't currently support Schnorr signatures and no complete
design for it has been proposed (although such a proposal may not be far
off), so this talk describes proof-of-concept results from a partial
implementation of scriptless-scripts that's compatible with Bitcoin's
current keys and signatures, ECDSA.  The talk especially focuses on the
use of scriptless scripts in payment channels such as those used by
Lightning Network, with some impressive savings in the size of scripts
and witness data---savings which increase the number of channels that
can be opened or closed in a block and which reduces the amount of
transaction fee paid by users of Lightning Network payment channels.

*[video][vid scriptless ecdsa], [transcript][tx scriptless ecdsa], possible
now*

## Discussion wrap-up: the evolution of bitcoin script

*Discussion wrap-up presented by Olaoluwa Osuntokun*

This wrap-up of a two-hour discussion group briefly mentioned a large
variety of proposed changes to Bitcoin's Script language---far too many
to mention here even in summary.  However, a few changes were mentioned
as theoretically possible to accomplish in 2019 if the community is
willing to adopt them:

- **Schnorr signature scheme:** an opt-in feature providing smaller
  signatures in all cases, much smaller public key and signature data
  for cooperative multisigs, and faster validation.  Easier
  compatibility with scriptless scripts.  See Pieter Wuille's [proposed
  BIP][schnorr pre-bip].

- **SIGHASH_NOINPUT_UNSAFE:** the ability to create spends without
  explicitly referencing which output you want to spend.  Allows
  creating more efficient payment channels using the [Eltoo protocol][]
  that also makes it easy for each channel to support up to 150
  participants.  See [BIP118][].

- **OP_CHECKSIGFROMSTACK:** makes it possible to create covenants that
  restrict what outputs a particular coin can be spent to.  For example,
  you could require that your bitcoins can only be sent to an address
  that requires giving you 1,000 blocks (1 week) to evaluate the
  situation and possibly send them back to a whitelisted address before
  they can be spent to any non-whitelisted address.  (The presenter
  notes that some developers are opposed to enabling this for
  fungibility reasons, although alternative approaches may be more
  acceptable.)

- **Fixing the time warp bug:** a set of miners controlling a majority of
  the hash rate can currently manipulate Bitcoin's difficulty adjustment
  algorithm to allow them to consistently create more than one block
  every ten minutes even without increasing overall hash rate.  There's
  at least one simple proposal to reduce the amount of manipulation
  possible without breaking older software or mining equipment.  See the
  recent [email thread][bitcoin-dev timewarp] on the Bitcoin-Dev mailing
  list.

- **Explicit fees:** currently fees in Bitcoin are implied by the difference
  between the value of the aggregated inputs and the aggregated outputs.
  However, the transaction could alternatively explicitly commit to the
  fee and allow one of the outputs to be set to the difference between
  the value of the aggregated inputs and the explicit fee plus all the
  other outputs.  This could be useful for rewarding Lightning Network
  watchtowers that send breach remedy transactions on behalf of offline
  users, or it could be useful for fee bumping group transactions.

However, the presenter concluded this section of the wrap-up by saying,
"the only people who have comfort with soft-forks are unlikely to
propose a soft-fork and produce software that would be adopted. People
are going to fight anything that adds anything, especially considering
the recent [CVE][CVE-2018-17144]. People are going to be for the next 6
months significantly more conservative. It's going to be another 6
months before people are even thinking about it. I don't think we're
going to get any new soft-forks in the next year."

*(no video), [transcript][tx script]*

## Footnotes

[^freq-pow-waste]:
    When a miner creates a new block at the tip of the chain, he can
    begin working on the next block immediately---but every other miner
    is still working on an old block until they receive the new block,
    meaning their proof of work during that brief period of time is
    wasted (it neither increases network security nor provides the
    miners with financial compensation).  Miners with more hash rate
    produce more blocks on average, so they get the head start more
    often and less of their proof of work is wasted.

    For two perfectly fair miners half a world apart, the network delay
    can be about one second, meaning a small miner far away from most
    other miners is likely to only be productive for 599 of the average
    of 600 seconds (ten minutes) between blocks.  A 1/600 loss of
    efficiency (0.16%) is not too bad, but if the block frequency were
    increased, the loss of efficiency would increase also: at one block
    per minute, the loss of efficiency would be 1.66%; at one block per
    six seconds, 16.66%.

    The small miner could increase his efficiency by moving closer to
    other miners or even completely eliminate the efficiency loss by
    merging with them, but this is the mining centralization that it's
    essential to avoid in Bitcoin if we want to prevent miners from
    being able to effectively restrict (censor) which transactions are
    included in blocks.

{% include references.md %}

[videos]: https://tokyo2018.scalingbitcoin.org/#remote-participation
[transcripts]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/
[vid subsidy]: https://youtu.be/y8hJ0VTPE34?t=39
[tx subsidy]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/playing-with-fire-adjusting-bitcoin-block-subsidy/
[vid forward blocks]: https://youtu.be/y8hJ0VTPE34?t=3744
[tx forward blocks]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/forward-blocks/
[paper forward blocks]: http://freico.in/forward-blocks-scalingbitcoin-paper.pdf
[vid bls msig]: https://youtu.be/IMzLa9B1_3E?t=29
[tx bls msig]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/compact-multi-signatures-for-smaller-blockchains/
[paper bls msig]: https://eprint.iacr.org/2018/483.pdf
[vid accumulators]: https://youtu.be/IMzLa9B1_3E?t=3522
[tx accumulators]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/accumulators/
[vid scriptless ecdsa]: https://youtu.be/3mJURLD2XS8?t=3624
[tx scriptless ecdsa]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/scriptless-ecdsa/
[tx script]: http://diyhpl.us/wiki/transcripts/scalingbitcoin/tokyo-2018/bitcoin-script/
[musig paper]: https://eprint.iacr.org/2018/068
[schnorr pre-bip]: https://github.com/sipa/bips/blob/bip-schnorr/bip-schnorr.mediawiki
[pairing-based cryptography]: https://en.wikipedia.org/wiki/Pairing-based_cryptography 
[bls sigs]: https://en.wikipedia.org/wiki/Boneh%E2%80%93Lynn%E2%80%93Shacham
[scriptless scripts transcript]: https://scalingbitcoin.org/transcript/stanford2017/using-the-chain-for-what-chains-are-good-for
[eltoo protocol]: https://blockstream.com/2018/04/30/eltoo-next-lightning.html
[bitcoin-dev timewarp]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-August/016316.html
