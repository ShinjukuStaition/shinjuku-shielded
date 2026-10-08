# Privacy model

This page says who sees what. Every claim carries its limit. If a limit here
is wrong, report it: [SECURITY.md](../SECURITY.md).

Two things are separate:

1. **On-chain privacy.** What the Solana chain shows. The scheme decides it.
2. **Network privacy.** Who sees your IP address and your requests. The
   transport decides it (default, VPN, or `--tor`).

`--tor` does not change on-chain privacy. A scheme does not hide your IP.

## Who sees what, for each scheme

"Our facilitator" means our server: the facilitator, the relayer, the memo
service, and the RPC relay.

### `shielded-exact` (payer hidden in the pool)

| Party | Sees | Does not see |
|---|---|---|
| Chain | A pool program call signed by our fee payer, and its time. | The payer, the amount, and any token movement. |
| Seller | Your request, the price, the time, and your IP unless you use `--tor`. | Your wallet's key. |
| Our facilitator | What it processes: the payment, its time, and the IP of each request (a Tor exit with `--tor`). | Your keys and seed. |
| RPC | With our relay (the default): which accounts your wallet reads, the transactions it sends, and your IP unless `--tor`. With your own RPC: the same, at your provider. | Your keys. |
| MCP host and model provider | Every URL, body, price, and paid answer the agent handles. | Your keys and passphrase: they stay in the wallet process. |

Limit: the payer is hidden only among the pool's other users. See "Privacy
needs a crowd" below.

### `confidential` (hidden amount, Lane B)

| Party | Sees | Does not see |
|---|---|---|
| Chain | 5 transactions from our fee payer, the payer's pocket token account, a one-time account, and the time. | The amount and the seller. |
| Seller | The payer's pocket address, the amount, and the time. | The payer's account with us. |
| Our facilitator | The payer account, the seller, the amount, the URL, and the time of each payment. | The contents of a slot: we cannot decrypt a slot. |
| RPC | With our relay (the default): which accounts the wallet reads. | Your keys. |
| MCP host and model provider | Not used: the MCP does not pay `confidential`. | |

Limits: Lane B needs a session token from a sign-in with a key that you
make. That account names you to our facilitator for every Lane B step. Who
paid rests on the V0b pool's crowd, which is small.

### `exact` from a pocket

| Party | Sees | Does not see |
|---|---|---|
| Chain | The pocket, the seller, the amount, and the time. | Your wallet's own key. |
| Seller | The pocket, the price, the time, and your IP unless `--tor`. | Your wallet's own key. |
| The facilitator that settles | The pocket, the seller, the amount, and the time. This can be the seller's own facilitator, not ours. | Your wallet's own key. |
| MCP host and model provider | Every URL, body, price, and paid answer. | Your keys. |

Limits: the pocket was filled from the pool. With few depositors, an
observer who lists the pool deposits can link the pocket to its depositor.
Each seller gets its own pocket, so one pocket never pays two sellers.

### Money in and money out

| Step | The chain shows | The chain does not show |
|---|---|---|
| A deposit from your own key (`deposit`, MCP `wallet_shield`) | Your key, the amount, and the time. | Your later payments. |
| A relayed deposit from a one-time address (`add-funds`) | The one-time address, the amount, and the time. | Your wallet's own key. |
| An unshield | The amount, the recipient, and the time. | Which deposit paid it. |

The funding route also sees you. An exchange knows you, the amount, and the
address. ChangeNOW sees the amount and the address. Never fund a one-time
address from a wallet that you use elsewhere: that transfer links you.

## Privacy needs a crowd

A shielded pool hides you among its other users. With few users, time and
amounts can link a deposit to a payment. When you are the only user (k=1),
waiting buys nothing.

Our pools are new and have few users. This is the main limit of Shinjuku
Shielded today. Some help exists:

- `pocket fill` waits after your newest deposit until 3 other pool events
  land, or until a cap (`--dwell-minutes`, default 1). Its output says how
  many other events there were.
- Pockets move uniform 1 USDC chunks.

These reduce the risk. They do not remove it.

## Your network: default, VPN, or `--tor`

| Mode | Who sees your IP | Who sees your requests |
|---|---|---|
| Default (`https://pay.shinjukustaition.com`) | Our server and the seller. | Our server and the seller. No CDN: this host skips Cloudflare. |
| A VPN or `--proxy` | The VPN company. We and the seller see the VPN's address. | The VPN company sees every request (which seller, which accounts). We and the seller see them too. |
| `--tor` | No single party sees both who you are and what you request. | Our onion service and the seller, without your IP. |

- `--tor` needs one line in `torrc`:
  `HTTPTunnelPort 127.0.0.1:9080 IsolateDestAddr`. The wallet checks that it
  reaches the network through Tor before it sends any other request.
- With `--tor`, every request to our facilitator goes to its onion service.
  It adds about 1 to 2 seconds per request.
- Our RPC relay calls the Solana RPC provider from our own server. The
  provider never sees your IP when you use our relay.
- A keyed RPC URL names its account holder. With `--tor`, use an RPC URL
  that has no API key in it, or use our relay.
- `--ohttp-relay <url>` sends RPC and x402 requests as Oblivious HTTP. The
  relay sees your IP, not the request. It helps only if someone other than
  us runs the relay.

### Cloudflare is not in the payment path

`https://pay.shinjukustaition.com` is the wallet's default facilitator. It
skips Cloudflare, so no CDN sees your IP or your requests. Our server still
does. `https://shinjukustaition.com` (the main site) is behind Cloudflare.

## Logs

- Our web server removes the client IP, port, and headers from its log
  lines.
- The RPC relay keeps no request log.
- The opt-in seller list holds seller data only: no buyer data and no
  payment counts. The list routes take no search terms, so we never learn
  what a buyer looks for.

Limit: our process sees each request in memory while it answers it. "No
log" is a policy. You must trust it. `--tor` removes your IP from what we
can see.

## What our operator can do

- **See what it processes.** Our server sees your deposits, payments, and
  exits, their times, and the IP of each request (a Tor exit with `--tor`).
- **See V0b amounts and block V0b exits.** Our key service sees every amount
  in the V0b pool. It can block every V0b exit. No guardian or escape path
  exists yet. Moving the key service into a secure enclave is in progress.
- **One fee payer.** Our relayer pays every V0b transaction, so the chain
  groups those accounts under one fee payer. That alone does not link a
  deposit to a pocket. Timing and amounts can.
- **Setup keys.** The proving keys came from single-operator setups. No
  multi-party ceremony has run.
- **No audit.** No independent audit of the circuits, the verifiers, or the
  binding has run.

## Your own traces

- A keyed RPC URL names you.
- The timing of your requests links them.
- Everything you sign on chain is public.
- Over MCP, a local model removes the model provider as an observer.
