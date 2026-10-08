# Changelog

Wallet releases (`shinjuku-wallet.mjs`), newest first. Each release ID is
the first 8 hex characters of the file's SHA-256. The full hashes are in the
Releases table of the [README](README.md). Each release keeps everything in
the release before it.

## 2026-10-08

- `11fe6b40` (current): one command sets everything up. `npx -y shinjuku-shielded mcp`
  on a machine with no wallet offers setup (a y/n prompt in a terminal; in an
  agent app, a "Create a wallet?" confirmation). Setup creates the wallet with
  a generated private passphrase, downloads the proof tools and checks them
  against a set pinned inside the wallet, and writes a config file, so `mcp`
  needs no flags. On npm as `shinjuku-shielded@0.2.0`.
- `1a336885`: a relayed exit proof that is still valid and was
  never sent is sent again at once, instead of waiting for it to expire.
  On npm as `shinjuku-shielded@0.1.0` and `0.1.1`.
- `2c2f9874`: the MCP handles everything itself. `wallet_shield` is on by
  default. `wallet_unshield` sends to an address you listed at once, or to any
  other address only after you confirm it in your MCP client. The wallet's own
  key is refused as a destination. The wallet refreshes itself and writes an
  encrypted backup after each shield and unshield. Sending your own money out
  no longer counts against the wallet's payment budget.
- `c4a50798`: pocket reshield: the public USDC left in a pocket returns to the
  shielded balance.
- `b7d6f394`: MCP `wallet_balance` has one meaning for `unshieldedAtomic`
  (the public USDC on the wallet key). The total of finished unshields is now
  `detail.unshieldedTotalAtomic`.
- `63316238`: a payment never spends a note that an unfinished exit will
  spend. An exit whose note another payment spent is marked released, not
  stuck.
- `565c4f87`: an unshield waits for the next pool anchor instead of stopping.
  An exit proof that expired before it was sent is proved again.

## 2026-10-07

- `57bfd2fd`: with `--tor`, the Tor circuit to your own RPC is warmed up in
  parallel, so a payment starts faster.
- `768001dd`: a refused payment whose input another settled payment spent no
  longer blocks the wallet.
- `b4c514cd`: one chain clock read before a payment is sent. The wait for a
  single funded account names `add-funds --split`.
- `061adadb`: the quote-time check uses one chain clock read.
- `721b8d65`: the saved deployment check gets one more try after a network
  failure.
- `97d6703d`: a faster, parallel first read of the item card keeps the
  correct answer.
- `3765885f`: the proof inputs are built once per payment, not once per
  input.
- `c6afbdbe`: compact pool history first, with retries and timeouts over
  Tor. The default facilitator is now `https://pay.shinjukustaition.com`,
  which skips Cloudflare.
- `6909057a`: memos are read ahead, and Tor circuits warm up early.
- `bf2652a3`: one bulk read for the buffer lists.
- `dc92be17`: money decisions bind to the on-chain pool root. A faster proof
  step. A UTF-16 passphrase file is refused with a fix. New `v0b unshield`.
- `551f8ad9`: new `keep-warm`. With `--tor`, every request to our
  facilitator goes to its onion service.
- `33d54d54`: timing marks before a payment, a 10 s limit on the item card
  read, and the epoch anchor read ahead of the 402.
- `0a638498`: retries for the item card, and a new Tor circuit after a
  history timeout.
- `c7abfaaf`: the history cache stops at its snapshot.
- `a66228ab`: `laneb pay-url` stages the proof accounts first, so the
  seller's settle sends one transaction. A smaller proof-tools set
  (`82b2d870`). Bulk history reads.
- `c0fb991f`: scheme `confidential` for x402 sellers (`laneb pay-url`,
  `laneb seller info`, with the amount proof). New `curl` and `mcp`
  commands, with spend caps. No RPC of your own needed: the facilitator's
  relay is the default. The next payment no longer waits for the previous
  payment's finality.
- `cbd97cea`: the `laneb` command group (Lane B, hidden amounts): payee
  register, slots, quote, pay, receipts, and slot return.

## 2026-10-06

- `833aeed3`: a faster wait before the history walk, one walk reused,
  parallel buffer reads, and a new blinded point on a requote.

## 2026-10-05

- `f561eccf`: history proxy phase 2 and the production onion pinned in the
  wallet. A hint when a quote ended. Self-payments walk the history before
  the 402.

## 2026-10-02

- `68aac3c1`: the first release in this table. With its proof tools, a
  filled pocket pays any Solana x402 `exact` endpoint that takes USDC,
  whichever facilitator settles it.
