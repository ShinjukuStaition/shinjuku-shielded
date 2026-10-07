# Shinjuku Shielded

Shinjuku Shielded is a privacy-only x402 payment facilitator on Solana.
An agent pays an x402 endpoint from a balance that it holds itself. The
chain does not show who paid.

- **Privacy-only.** We do not offer a cheaper path that is not private.
- **You keep your keys.** The wallet makes your keys on your machine. We
  never hold them, and we never hold your money.
- **We relay and pay the fees.** Our relayer sends your relayed deposits and
  exits and pays their network fees. Our facilitator pays the network fee of
  each payment that it settles.
- **One balance.** Your Shinjuku Shielded balance is a set of redeemable
  notes that you hold. The wallet shows it as one number.
- **Standard sellers work.** A seller with standard x402 `exact` middleware
  that points at our facilitator gets paid with no code change. (An
  unmodified seller was paid this way on production on 2026-09-29.)

## Which endpoints you can pay

- **A seller that settles through our facilitator.** The wallet pays it from
  your shielded balance, and our facilitator settles the payment.
- **Any other Solana x402 `exact` endpoint that takes USDC, whichever
  facilitator settles it.** First fill a pocket (`pocket fill`). Then `pay`
  pays the seller from that pocket. Our relayer sends your deposit and the
  private exit into the pocket. The seller's own facilitator settles the last
  step, the payment from the pocket to the seller. On 2026-10-02 a test paid
  two such endpoints with real USDC on Solana mainnet: one that Coinbase's
  facilitator settles and one that PayAI's facilitator settles. This path
  needs wallet release `68aac3c1` or later.

The 3D market game [Shinjuku Undermarket](https://shinjukustaition.com) is
our first customer and our live demo.

This repository holds documentation and the published SHA-256 hashes of the
wallet file and its proof tools. It holds no server code, no circuit code,
and no wallet source code.

## Status

- Hidden amounts on mainnet: live, as a capped alpha.
- Pocket payments to sellers that settle through our facilitator: live.
- Pocket payments to any Solana x402 `exact` endpoint that takes USDC: live
  (wallet release `68aac3c1` and later).
- The wallet file and its proof tools, served from our site and our onion
  service with SHA-256 hashes: live.
- Key ceremonies: single-operator setup ceremony for the V0b keys done
  (2026-10-06); a multi-party ceremony: not yet.
- Operator-blind keys (our key service in a secure enclave): in progress.
- Independent audit: none yet.

## Releases

Each release is one file, `shinjuku-wallet.mjs`, and a set of proof tools.
Our website serves the files and their SHA-256 hashes at
`https://shinjukustaition.com/wallet/<sha8>/` and
`https://shinjukustaition.com/proof-tools/<sha8>/`. This repository is a
second, separate place for those hashes: each release adds its row here.

The website serves only the newest release: use the last row of the table.
Some short-lived releases served between 2026-10-05 and 2026-10-07 have no row
here; only the last row is served, and only its hash matters for a new install.

| Released (UTC) | Wallet release | Wallet file SHA-256 | Bytes | Proof tools | SHA-256 of the proof-tools file list | Proof-tools files |
|---|---|---|---|---|---|---|
| 2026-10-02 | `68aac3c1` (no longer served) | `68aac3c1f7bcf9e21c44637c5db4cef3ad8ed52732cd5634f0884d1417a78b1d` | 9,476,013 | [`864898cb`](https://shinjukustaition.com/proof-tools/864898cb/SHA256SUMS) | `864898cb1a7db74c54bc567307795ba154f2b8ed70e36a1336bf8c6c06c5c702` | 29 (183,830,695 bytes) |
| 2026-10-05 | `f561eccf` (no longer served) | `f561eccf5c357731d9be5bc06a02accbc52e6368dd95ce95efb85542c039acc7` | 9,548,479 | [`864898cb`](https://shinjukustaition.com/proof-tools/864898cb/SHA256SUMS) | `864898cb1a7db74c54bc567307795ba154f2b8ed70e36a1336bf8c6c06c5c702` | 29 (183,830,695 bytes) |
| 2026-10-07 | `833aeed3` (no longer served) | `833aeed35eca3ad85a9b8b949b2452945a071081765677060c509fd786976ff4` | 9,602,171 | [`c777fa3e`](https://shinjukustaition.com/proof-tools/c777fa3e/SHA256SUMS) | `c777fa3eac5f874d0b8b07a4ed352ece37825412b9dfde206222f66a31a12252` | 29 (184,817,703 bytes) |
| 2026-10-07 | `cbd97cea` (no longer served) | `cbd97cea4c71cdf0b27df3b59d687cd65e4b6d93965e36a6a8f3f5a525c12c42` | 9,798,568 | [`c777fa3e`](https://shinjukustaition.com/proof-tools/c777fa3e/SHA256SUMS) | `c777fa3eac5f874d0b8b07a4ed352ece37825412b9dfde206222f66a31a12252` | 29 (184,817,703 bytes) |
| 2026-10-07 | `c0fb991f` (no longer served) | `c0fb991f0a59133c389486af11b40f0565732331152b9b8b82a84e62999d9bc3` | 9,932,761 | [`c777fa3e`](https://shinjukustaition.com/proof-tools/c777fa3e/SHA256SUMS) | `c777fa3eac5f874d0b8b07a4ed352ece37825412b9dfde206222f66a31a12252` | 29 (184,817,703 bytes) |
| 2026-10-07 | `a66228ab` (no longer served) | `a66228aba12d70e4f2cfc0fa83122bb09073bbd5734aec74a0b16ddebad044a9` | 9,937,880 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |
| 2026-10-07 | `c7abfaaf` (no longer served) | `c7abfaafb1fbcdae82adeb927b4322d79582cd45af5410f5af532141e2d74004` | 9,944,079 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |
| 2026-10-07 | `0a638498` (no longer served) | `0a6384984b20f971739f29ba3b7d1d54ff9f0861e79f330b7018812c06ba5e08` | 9,947,337 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |
| 2026-10-07 | `33d54d54` (no longer served) | `33d54d540882fdbe8d2cfaf85cfee3a34084f6de6f30a0ee7416f1996fb1f176` | 9,949,930 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |
| 2026-10-07 | `551f8ad9` (no longer served) | `551f8ad94c8a9eb17edd1322c22381e30b363d6e9478869ca92ee753162356ce` | 9,959,998 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |
| 2026-10-07 | [`dc92be17`](https://shinjukustaition.com/wallet/dc92be17/shinjuku-wallet.mjs) | `dc92be17c691b053e6a63e7b60c64d08952e56b375d8fb9010e735083d200562` | 9,994,933 | [`82b2d870`](https://shinjukustaition.com/proof-tools/82b2d870/SHA256SUMS) | `82b2d8709f28cfdd65c9a03707af4811b8d6e77b1231b4e1de6dd2d84f209c3b` | 26 (144,319,243 bytes) |

Beside each wallet file, the website also serves `LICENSE.txt` (the
copyright notice), `THIRD_PARTY_NOTICES.txt` (the open-source licenses inside
the file), and `manifest.json` (the source commit and every bundled package).
The manifest's source commit names our private development repository. This
public repository publishes the hashes, not the source code. Every release is
reproducible: two independent builds (Windows and Linux) of that commit give
the same SHA-256, and our server refuses to start if the file it serves differs
from the pinned hash.

## Get the wallet

The wallet needs Node.js 22 or later and nothing else.

### 1. Download

```sh
curl -fsSLO https://shinjukustaition.com/wallet/<sha8>/shinjuku-wallet.mjs
```

On Windows, type `curl.exe`, not `curl` (in Windows PowerShell, `curl` is a
different command).

Over Tor, from our onion service (production):

```sh
curl --socks5-hostname 127.0.0.1:9050 -fsSLO \
  http://2kfhlfuyuwvhmibjrpxqsg4nrbcxasjgjq7kmnfzgfwzptkhhznz3dad.onion/wallet/<sha8>/shinjuku-wallet.mjs
```

### 2. Check the SHA-256

Compare the file with the hash in the Releases table above. Go on only when the check
prints `OK`. On any other result, delete the file and do not run it.

Linux:

```sh
echo "<sha256>  shinjuku-wallet.mjs" | sha256sum -c -
```

macOS:

```sh
echo "<sha256>  shinjuku-wallet.mjs" | shasum -a 256 -c -
```

Windows (PowerShell):

```powershell
if ((Get-FileHash .\shinjuku-wallet.mjs -Algorithm SHA256).Hash -eq "<sha256>") { "OK" } else { "MISMATCH" }
```

Windows (cmd.exe): `certutil -hashfile shinjuku-wallet.mjs SHA256` prints
the hash. It must equal the SHA-256 in the table.

A match proves that your copy is complete and equals the file whose hash we
published here. It does not prove who made the file.

### 3. Read the help

```sh
node shinjuku-wallet.mjs help
node shinjuku-wallet.mjs help init
```

`help <command>` prints a working example, every flag, and the next step.
Exit code 0 means done. Exit code 1 means the command ran and failed (one
line says why). Exit code 2 means the command line is wrong (it shows a
working example).

With the wallet file alone, these commands work: every `help` page, `init`,
`backup-export`, `backup-verify`, `restore`, `balance` (without
`--refresh`), and `v0b selfcheck`. The money commands also need the proof
tools (next section).

## Get the proof tools

`add-funds`, `deposit`, `recover`, `balance --refresh`, `pay`, `buy-item`,
`unshield`, and `pocket fill` need the proof tools of the facilitator that you
use: two manifests, the facilitator's public profile, Linux x86-64 provers,
and the signed proving parameters (29 files, about 184 MB). They need wallet
release `68aac3c1` or later.

The provers are Linux programs.

- **Linux:** run the steps below as written.
- **Windows:** install WSL (Ubuntu). Run the steps in a WSL shell, in a folder
  on a Windows drive (for example `/mnt/c/Users/<you>/shinjuku-proof-tools`).
  Then run the wallet from Windows with the `C:\` paths. The wallet runs each
  prover through `wsl.exe`.
- **macOS:** not supported. Use a Linux machine or a Linux container.

```sh
mkdir shinjuku-proof-tools && cd shinjuku-proof-tools
base=https://shinjukustaition.com/proof-tools/<sha8>
curl -fsSO "$base/SHA256SUMS"
echo "<sha256 of the file list>  SHA256SUMS" | sha256sum -c -
while read -r sum path; do mkdir -p "$(dirname "$path")"; curl -fsS -o "$path" "$base/$path"; done < SHA256SUMS
sha256sum -c --strict --quiet SHA256SUMS && echo ALL_OK
chmod +x bin/* house/bin/* bundle/bin/*
```

Go on only when the list check prints `OK` and the last check prints
`ALL_OK`. Then give the wallet:

- `--proof-tools shinjuku-proof-tools/proof-tools.json`
  (`--payer-proof-tools` for `pocket fill` and `unshield`),
- `--exit-proof-tools shinjuku-proof-tools/exit-proof-tools.json`,
- `--profile shinjuku-proof-tools/public-profile.json`.

The wallet checks the SHA-256 of each binary again before every run.

The `v0b` money commands (the hidden-amount vault) also need the
`ctvault_v0b` prover. We do not publish it yet.

## Quick start

The full guide is the wallet's own help. The steps below follow it.

### What you need

- Node.js 22 or later. (The two `privacy-cash` commands need Node.js 24 and
  the Privacy Cash SDK, which is not part of the wallet file.)
- The proof tools (previous section).
- A Solana RPC URL in a private file, `rpc.txt`. With `--tor`, use an RPC
  URL that has no API key in it: a keyed URL names its account holder.
- A private folder for the wallet, and a passphrase of 8 or more characters.
- The public identities (program, pool, mint, network, facilitator): run
  `node shinjuku-wallet.mjs help`. Each release names its own facilitator.

```sh
export SHIELDED_WALLET_HOME=<a private folder>
export SHIELDED_WALLET_PASSPHRASE=<your passphrase>
W=shinjuku-wallet.mjs
```

Keep the same passphrase in `passphrase.txt` and your RPC URL in `rpc.txt`.

### Hide your IP

Add `--tor` to any command. Start Tor with one line in `torrc`:
`HTTPTunnelPort 127.0.0.1:9080 IsolateDestAddr`. The wallet checks that it
reaches the network through Tor before it sends any other request, and it
refuses any connection that does not go through the proxy.

### 1. Create the wallet

```sh
node $W init --pool $POOL --network $NETWORK --asset $MINT \
  --binding carbon-shielded-exact/svm-v3 --max-payment 30000 --max-cumulative 60000 \
  --backup-out recovery.txt
```

`init` writes your recovery text (the seed and the Solana secret key) only
to the new `--backup-out` file, never to the screen. Copy that file to two
offline places, then delete it from this machine. Keep the
`solanaFeePayer` and `seedFingerprint` that `init` prints beside it.

### 2. Back up (after every step that moves value)

```sh
node $W backup-export --pool $POOL --to /backups/01-created
node $W backup-verify --pool $POOL --backup-dir /backups/01-created
```

### 3. Add funds privately

```sh
node $W help add-funds
```

`add-funds` runs every funding step in order and waits between them. It ends
with `ready` and your balance. Three routes:

- `exchange`: withdraw the exact amount from an exchange to the one-time
  address that the wallet prints. The exchange knows you, the amount, and
  the address.
- `changenow`: a fixed-rate ChangeNOW swap to the one-time address, paid
  with Monero (best), BTC, or ETH. ChangeNOW sees the amount and the
  address.
- `privacy-cash`: your own wallet pays into the Privacy Cash pool; at least
  24 hours and 10 other deposits later, Privacy Cash pays the one-time
  address. It takes about a day.

Never fund the one-time address from a wallet that you use elsewhere. That
transfer links you.

### 4. Pay

```sh
node $W help pay
node $W help pocket fill
```

- A seller that settles through our facilitator: `pay` pays it from your
  shielded balance at the seller's price.
- Any other Solana x402 `exact` endpoint that takes USDC: run `pocket fill`
  first. The pocket fills ahead of the payment, so the payment itself does
  not wait for the privacy steps. Then `pay` pays from the pocket. The first
  payment binds the pocket to that seller; later payments reuse it.

### 5. Check your balance and withdraw

```sh
node $W help balance
node $W help unshield
```

`unshield` sends part of your balance to a public USDC account; our relayer
sends the exit and pays its fees. Run `backup-export` again after each step
that moves value.

## The self-custody model

- Your keys are a Solana key and a shielded seed. `init` makes both on your
  machine. No account registration, no Ethereum key, and no identity
  signature.
- We never receive your keys, seed, passphrase, or recovery file. We cannot
  recover them. If you lose the seed and the recovery file, your funds are
  lost.
- `restore` rebuilds the wallet from the recovery file. `recover` reads your
  shielded notes from your seed, the public chain history, and the public
  memos.
- The wallet writes deposit, payment, and unshield steps to its log before
  it sends them. After an interruption, run the same command again. The
  help of each command says how it resumes.
- Our relayer pays the network fees of relayed deposits and exits. A
  self-broadcast deposit and the first Privacy Cash step are paid by your own
  key.
- We never ask for your seed, passphrase, or recovery file. Anyone who asks
  is not us.

## What the privacy covers, and what it does not

What the chain does not show: who paid a seller from a Shinjuku Shielded
balance. In the confidential-vault pool (V0b, the wallet's `v0b` commands),
the deposit into the pool and the exit from it carry no amount bytes on
chain.

What it does not hide:

- **Our server sees what it processes**: your deposits, payments, and exits,
  their times, and the IP address of each request (a Tor exit with
  `--tor`).
- **Deposits are public.** A deposit's amount and time are public. With few
  depositors in a pool, time can pair your deposit with your later payments.
  When you are the only user (k=1), waiting buys nothing. Our pools are new
  and have few users.
- **Amounts outside the vault are public.** In the V0b pool, the wrap,
  unwrap, and withdraw amounts are public, and the deposit amount can be
  inferred from the public wrap one transaction earlier. Pockets move public
  1 USDC chunks, and the seller's price is public.
- **Another facilitator sees its last step.** When the seller's own
  facilitator settles the payment from a pocket, that facilitator sees the
  pocket, the seller, the amount, and the time, as the chain does.
- **Our key service sees V0b amounts.** It sees every V0b amount, and it can
  block every V0b exit: there is no guardian or escape path yet.
- **One fee payer.** Our relayer pays every V0b transaction, so the chain
  groups those accounts under one fee payer. That alone does not link a
  deposit to a pocket; timing and amounts can.
- **Your own traces.** A keyed RPC URL names you. The timing of your
  requests links them. Everything you sign on chain is public.

Keys, audits, and limits:

- The production keys of the main shielded pool came from a
  single-operator offline setup. No multi-party ceremony has run.
- The V0b proving keys come from a single-operator setup ceremony run on
  2026-10-06; the V0b program was upgraded to verify only proofs made with
  those keys, and a new V0b pool opened with them (wallet release `833aeed3`
  or later proves with them). No multi-party ceremony has run yet. The older
  V0b pool, made with test-only keys, accepts exits only.
- No independent audit of the circuits, the verifiers, or the binding has
  run.
- Do not expect a cold wallet to settle at once: on 2026-09-23 a payment
  took 1.5 to 4 minutes. Pockets are filled ahead of a payment.

## Copyright

Each wallet file comes with this notice (`LICENSE.txt` beside the file):

> Shinjuku Shielded wallet
> Copyright (c) 2026 Shinjuku Shielded. All rights reserved.
>
> The open-source packages inside this file keep their own licenses: see
> THIRD_PARTY_NOTICES.txt.

`THIRD_PARTY_NOTICES.txt` beside each release lists every open-source
component inside the file, with its license text. One of them,
`rpc-websockets`, is under LGPL-3.0-only.

## Security

See [SECURITY.md](SECURITY.md).
