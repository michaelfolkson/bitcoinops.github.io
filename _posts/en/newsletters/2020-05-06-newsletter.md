---
title: 'Bitcoin Optech Newsletter #96'
permalink: /en/newsletters/2020/05/06/
name: 2020-05-06-newsletter
slug: 2020-05-06-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter FIXME:harding

## Action items

FIXME:harding

## News

FIXME:harding

## Field report: running a Lightning node in an enterprise environment

{% include articles/suredbits-enterprise-ln.md %}

## Releases and release candidates

FIXME:harding update to latest versions Tuesday afternoon

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

- [Bitcoin Core 0.20.0rc1][bitcoin core 0.20.0] is a release candidate
  for the next major version of Bitcoin Core.

- [LND 0.10.0-beta.rc5][lnd 0.10.0-beta] allows testing the next major
  version of LND.

- [C-Lightning 0.8.2-rc2][c-lightning 0.8.2] is the newest release
  candidate for the next version of C-Lightning.

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo],
[C-Lightning][c-lightning repo], [Eclair][eclair repo], [LND][lnd repo],
[libsecp256k1][libsecp256k1 repo], [Rust-Lightning][rust-lightning repo],
[Bitcoin Improvement Proposals (BIPs)][bips repo], and [Lightning
BOLTs][bolts repo].*

*Note: the commits to Bitcoin Core mentioned below apply to its master
development branch and so those changes will likely not be released
until version 0.21, about six months after the release of the upcoming
version 0.20.*

FIXME:harding

- [Bitcoin Core #16528][] allows the `createwallet` RPC to create a
  wallet that uses [output script descriptors][topic descriptors] to
  derive just the particular scriptPubKeys that the wallet uses to
  receive payments.  This is a major improvement over the way legacy
  wallets scan for payments by deriving every type of script handled by
  the wallet for each public key in the wallet---a technique that dates
  back to the original Bitcoin 0.1 release's support for receiving to
  both P2PK and P2PKH scriptPubKeys.  Descriptor wallets should be more
  efficient (because they don't need to scan for unused script types),
  easier to upgrade to new types of script (e.g. for [taproot][topic
  taproot]), and easier to use with external tools (e.g. multisig
  wallets or [HWI][topic hwi]-compatible hardware wallets via
  [partially-signed bitcoin transactions][topic psbt]).

    By default, descriptor wallets use the popular [BIP32][] HD wallet
    paths specified by BIPs [44][BIP44], [49][BIP49], and [84][BIP84]
    rather than the non-standardized fully-hardened path used in legacy
    Bitcoin Core HD wallets.  A number of wallet RPCs are unavailable
    with descriptor wallets, either because they don't make sense with
    descriptors or because developers are still adapting them to new
    edge cases.  The merge of this PR early in the 0.21 development
    cycle and the decision to make descriptor wallets a non-default
    option will give the new features six months to mature before
    their expected release.

{% include references.md %}
{% include linkers/issues.md issues="16528" %}
[bitcoin core 0.20.0]: https://bitcoincore.org/bin/bitcoin-core-0.20.0
[lnd 0.10.0-beta]: https://github.com/lightningnetwork/lnd/releases/tag/v0.10.0-beta.rc5
[c-lightning 0.8.2]: https://github.com/ElementsProject/lightning/releases/tag/v0.8.2rc2
