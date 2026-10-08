# Sell with Shinjuku Shielded

This page sets up an x402 seller that takes private payments through our
facilitator. It uses public files only: the served wallet file and the stock
x402 middleware.

**What you can sell with today, from public files:**

| Scheme | Public seller setup | What stays hidden on chain |
|---|---|---|
| `confidential` (Lane B) | Yes: this page. | The amount and you, the seller. |
| `exact` (pocket payers) | Yes: your standard x402 `exact` middleware. See the last section. | Only the payer's wallet key. The amount and the seller are public. |
| `shielded-exact` | **Not public yet.** The seller package is not in the wallet file and not on npm. Today it needs our private source repository. | |

## Before you start

- Check that the facilitator serves the scheme:

  ```sh
  curl -fsS https://shinjukustaition.com/api/x402/supported
  curl -fsS https://shinjukustaition.com/api/x402/confidential/config
  ```

  `supported` must list `"scheme": "confidential"` on
  `solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp`. A `404` from `config` means
  this deployment does not offer Lane B. Stop there.
- Install and check the wallet: [README](../README.md), "Get the wallet".
  The `laneb` commands need a release whose `help` lists them:
  `node shinjuku-wallet.mjs help laneb`.
- Make the wallet: `init` ([README](../README.md), "Quick start").
- Get a session token. Sign in with an Ed25519 key that you make (no email,
  no password): section 2 of https://shinjukustaition.com/skill.md. Save the
  `sjp_` token in a private file, `play-token.txt`. It goes only to our Lane B
  routes, and the wallet never prints it.
- To return used slots, you need Linux or WSL with the V0b prover set
  (https://shinjukustaition.com/skill.md, section 7b, "The V0b prover set").
  macOS is not supported.

Every `laneb` command takes the same base flags:

```sh
--wallet <wallet file> --passphrase-file passphrase.txt \
  --service https://shinjukustaition.com --play-token-file play-token.txt
```

Add `--tor` to send every call through Tor. You need no RPC of your own: the
wallet reads the chain through our relay and says so on stderr.

## 1. Register once

```sh
node shinjuku-wallet.mjs laneb payee register <base flags>
```

This publishes two public keys made from your wallet seed. Your spending key
signs the registration. No secret leaves your machine.

## 2. Open slots

```sh
node shinjuku-wallet.mjs laneb slots open --count 4 <base flags>
```

A slot is a one-time confidential account that a payer pays into. Each paid
request uses one slot once. We pay the rent of each slot. The rent comes back
to us when you return the slot.

## 3. Get your seller settings

```sh
node shinjuku-wallet.mjs laneb seller info <base flags>
```

It only reads. It signs nothing and moves nothing. It prints:

- your `payTo`: your public spending key. It never appears on chain, because
  each payment goes to a one-time slot;
- the network and the shUSDC mint;
- `facilitatorUrl`: `https://shinjukustaition.com/api/x402`;
- your open slots and today's capacity;
- a checked middleware snippet for `@x402/core` 2.27.0.

## 4. Add the middleware

Paste the snippet that `laneb seller info` printed into your server. Use the
printed values, not the ones below. Its core has this shape:

```js
resourceServer.register("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp", {
  scheme: "confidential",
  defaultAssetTransferMethod: "confidential-transfer",
  // upfront: the middleware settles BEFORE your handler runs
  paymentFlows: { "confidential-transfer": { supported: ["upfront"], default: "upfront" } },
  parsePrice: async (price) => ({ amount: String(price.amount), asset: SHUSDC_MINT }),
  // extra.facilitator tells the payer's wallet where to reserve your slot
  enhancePaymentRequirements: async (r) => ({ ...r, extra: { ...r.extra, facilitator: "https://shinjukustaition.com" } }),
});
// route: { scheme: "confidential", network: "solana:5eykt…", payTo: "<your payTo>", price: { amount: "250000", asset: SHUSDC_MINT } }
```

- Set the route price in atomic units. 1 USDC = 1000000.
- The middleware calls `https://shinjukustaition.com/api/x402/verify` and
  `/settle`. They need no token.
- `/verify` checks the payment and the amount. It moves nothing.
- `/settle` answers `success` only when the payment is confirmed. Give the
  facilitator call at least 30 seconds.
- Our facilitator checks the amount (`amountCheck: "dleq-v1"`). A payment
  that transfers less than your price is refused, and nothing moves.

## 5. Check what arrived

```sh
node shinjuku-wallet.mjs laneb receipts --verify <base flags>
```

It prints `verified` for a slot only when the slot decrypts to the price and
received exactly one transfer.

## 6. Return used slots

```sh
node shinjuku-wallet.mjs laneb slot return --slot <slotId> <base flags> \
  --prover-dir shinjuku-v0b-prover --prover-sha256 <prover sha256> --params-dir shinjuku-v0b-prover/keys
```

This moves the slot's hidden balance into the V0b pool as your note and
closes the slot. We pay every fee. Lane B never makes a public withdrawal,
because a public withdrawal shows the amount. If you do not return used
slots, 4 or more used slots older than 7 days block new slots
(`slots_unreturned`).

## Capacity (alpha limits)

- At most 4 open slots per seller.
- At most 8 new slots per seller per UTC day, and 40 for all sellers.
- So one seller takes about 8 to 12 paid requests a day.
- With no free slot, a payer is refused (`no_free_slot`). Keep slots open.

## How buyers pay you

- **Scheme `confidential`:** the buyer fills a V0b pocket (`v0b deposit
  --relay`, then `v0b pocket fill`), then runs:

  ```sh
  node shinjuku-wallet.mjs laneb pay-url --url <your URL> --maximum-atomic 300000 --pocket 0 --out paid.bin <base flags>
  ```

  The buyer's `--service` must equal the `extra.facilitator` in your 402.
  The wallet refuses any other facilitator before it signs anything.
- **The MCP does not pay `confidential`.** The wallet's MCP server and the
  [shinjuku-mcp](https://github.com/ShinjukuStaition/shinjuku-mcp) setup pay
  `shielded-exact` and pocket `exact` only.

## What you see, and what we see

- You see the payer's pocket address, the amount, and the time. You do not
  see the payer's account.
- We see the payer account, you, the amount, the URL, and the time of each
  payment.
- The chain shows no amount and no seller. Who paid rests on the V0b pool's
  crowd, which is small.

## Get listed (opt-in)

Buyers find sellers in our list:
`GET https://shinjukustaition.com/api/x402/discovery/catalog.json`.
The list takes no search terms. Buyers filter it on their own side.

To join:

1. Publish `/.well-known/x402-discovery.json` on your own https origin:

   ```json
   {"schema":"x402-discovery/v1","resources":[{"url":"https://seller.example/paid","method":"GET"}]}
   ```

2. Send:

   ```sh
   curl -fsS -X POST https://shinjukustaition.com/api/x402/discovery/register \
     -H "Content-Type: application/json" -d '{"origin":"https://seller.example"}'
   ```

We list a URL only when its unpaid request answers a 402 that our
facilitator settles. For `confidential`, `extra.facilitator` must be our
origin. The answer names each refused URL with `next`. To delist a URL,
remove it from the file and register again. Limits: 50 URLs per origin, and
6 register calls per minute per IP.

A listed seller is public by its own choice. The register check reaches your
server from our server's IP.

## Other option: a standard `exact` seller

A seller with standard x402 `exact` middleware that points at our
facilitator gets paid with no code change. (An unmodified seller was paid
this way on production on 2026-09-29.) Our facilitator settles `exact` only
from pockets that our pool filled. So your buyers pay with the wallet's
`pay`, `curl`, or MCP, from a pocket.

Limit: the chain shows the pocket, you, the amount, and the time. Only the
payer's own wallet key stays hidden.
