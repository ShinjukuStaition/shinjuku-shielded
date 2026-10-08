# Proof: live mainnet transactions

Each row below is a real Solana mainnet transaction from our own test
runs. We read each one again on 2026-10-08 from the public RPC
(`https://api.mainnet-beta.solana.com`, `getTransaction`, commitment
`finalized`). Each one is finalized with `err: null`.

The amounts are small test amounts. These runs prove that the paths work.
They do not prove a large anonymity set: our pools are new and have few
users (see [privacy-model.md](privacy-model.md)).

## `shielded-exact`: a payment from the shielded balance

| Claim | Transaction | The chain shows | The chain does not show |
|---|---|---|---|
| A wallet paid a seller from its shielded balance over `--tor`, on production, 2026-10-07 19:39Z (22.7 s from start to the paid answer). | [`sq4NwDwU…WQp`](https://solscan.io/tx/sq4NwDwUASTns7wcg4CcXjLtZhAZH8bTqWodnXQZEc4P7vpiLutNhs12cUYPNJQ4DhUsP23K4WTqbVhGTWchWQp) | One call to the pool program (`8PYPw3FS…`), signed only by our fee payer (`6LhX4sv2…`). | Any token movement, the amount, the payer, the seller. |
| The same path, on production, 2026-10-07 19:16Z (21.9 s). | [`5khn7syZ…cs8n2`](https://solscan.io/tx/5khn7syZYS8m2mvmyjdJmuksehamPgUGRnqpSVCxtSnaFgXZQPae1cReowsR7HJv8uzPLoi7ef1B22hg2Hgcs8n2) | The same: one pool program call by our fee payer. | The same. |

## `confidential`: hidden amount (Lane B)

| Claim | Transaction | The chain shows | The chain does not show |
|---|---|---|---|
| A stock `@x402/express` 2.27.0 seller, pointed at our facilitator, was paid with scheme `confidential` on production, 2026-10-07 05:22Z. Price: 4,000 atomic (0.004 shUSDC). The seller's receipt check decrypted the slot to 4,000. | [`5AWtg8Ko…ai1t9`](https://solscan.io/tx/5AWtg8KoV9SxTLC5tRZB5b9ja3qwcedvcKGi4MBfhGz4UV5aiq7hotbUkvfytvvBE6S1rmoDgWERetNVN5Vai1t9) (the transfer; the last of 5 transactions) | A Token-2022 `confidentialTransfer` and 3 zero-knowledge proof checks. Signers: our fee payer and the payer's pocket. | The amount, the seller. |
| The same seller with the payment staged ahead: the seller's paid request took 1.591 s, on production, 2026-10-07 06:12Z. Price: 800 atomic. | [`3r8NK22H…JRovW`](https://solscan.io/tx/3r8NK22HGGrWRgYA81mumWG1v9NojRwYBMf3BjZ6qdEB1SuYdo1SRDppwBRyycSERgpfQ6XbANUXte3ajjEJRovW) | The same shape: one `confidentialTransfer` and 3 proof checks. | The amount, the seller. |
| Two fresh agents, each signed in with its own key, paid a Lane B quote on production, 2026-10-07 03:34Z. The payee ran every command over our onion service. Quote: 5,100 atomic. `laneb receipts --verify` said `verified`. | [`5hTvvNay…V6RLnZ`](https://solscan.io/tx/5hTvvNayfQKQ8zFnNdkJedssxvV7Ts92LBUWUsa94BnmDbd5Y8RHdFmMzzjh7YV5LufSM3x25pEFKCZW3bV6RLnZ) (one of 5 transactions) | One `confidentialTransfer` and 3 proof checks, signed by our fee payer and the payer's pocket. | The amount, the payee. |

Limit: one test key signs all three transfers above, so the chain links
these three test payments to each other. It does not show their amounts or
sellers. Our facilitator
saw the amount, the seller, and the URL of each payment.

## The MCP: shield, unshield, and pay from an agent

These runs used the wallet's local MCP server
([shinjuku-mcp](https://github.com/ShinjukuStaition/shinjuku-mcp)) on
2026-10-08.

| Claim | Transaction | The chain shows | The chain does not show |
|---|---|---|---|
| `wallet_shield` moved 1 USDC from the wallet's own key into the shielded pool. | [`hWzDiR1R…qrDizn`](https://solscan.io/tx/hWzDiR1RnC1gFXMMrCxRBCZ4R5dgMreWGX7vyQCTUTWkKaKnYvxmro6PQuE9KMYVbrq3NkNu9YaJzj7XaqrDizn) | The wallet's own key signs. 1,000,000 atomic USDC moves from its token account to the pool vault. A shield is public by design. | What the wallet later pays with it. |
| `wallet_unshield` sent 0.5 USDC to a public address: step 1, the self-payment. | [`5evVHz8X…Zr9uv`](https://solscan.io/tx/5evVHz8XvMdSYF76BQ1xv3TazwS9P38FEXp81gipNMMSqn61Rj7EUc1DdgpYz591wQbBZMWcpNVznixXPsfZr9uv) | One pool program call, signed only by our fee payer. | Any token movement, the amount. |
| The same unshield: step 2, the exit. | [`4yY2YXLS…apgF2i`](https://solscan.io/tx/4yY2YXLSj1EyUWDb7X9985KmyeNGQieF8skg3uN3sv3Pds3zSb42J5owDWMUTshui6XACkP4zwGxUmXXTVapgF2i) | 500,000 atomic USDC from the pool vault to the recipient. Our relayer signs and pays the fee. | The wallet that shielded: its key is not in the transaction. |
| `wallet_unshield` to an address that the human confirmed in the MCP client, 0.25 USDC, wallet release `1a336885`: the self-payment. | [`3jQyPgrA…QEWw`](https://solscan.io/tx/3jQyPgrA5AtFcqzyWHDWRcfNv66sM5qfCStxsByYDqGm7W5uRWkUMHpLoFUFm2jc98Aor7CXSToAChSHD7dWQEWw) | One pool program call, signed only by our fee payer. | Any token movement, the amount. |
| The same confirmed unshield: the exit. | [`4yA3qkHU…jCoh`](https://solscan.io/tx/4yA3qkHUto7e7iUGM9q6321avTm8aZQJ6zNMMh18SoAACnvgtvbWpVydry3mCq9iKaSZv92FfH43NJu557zjCoh) | 250,000 atomic USDC from the pool vault to the recipient. Our relayer signs and pays the fee. | The wallet that shielded. |
| `x402_pay` paid a standard `exact` seller from a pocket. | [`kX2zbbuR…P48wW`](https://solscan.io/tx/kX2zbbuRbskBQ2EnazyDBu7oZ7BtySVT7zDmtBn7ZivMm3guhLWrJGiPFFLzTEyxwTKUkeSAJjTJLSWLHYP48wW) | A standard USDC `transferChecked` of 1,000 atomic from the pocket to the seller, with a memo. Our `exact` fee payer (`zM8c2oE4…`) and the pocket sign. | The wallet's own key. |

Limits:

- An unshield shows the amount, the recipient, and the time. In these runs
  the shield and the unshields were minutes apart in a quiet pool. Timing
  and amounts gave weak cover.
- A pocket payment shows the pocket, the seller, and the amount.

## Check a transaction yourself

```sh
curl -s https://api.mainnet-beta.solana.com -H "Content-Type: application/json" -d '{
  "jsonrpc":"2.0","id":1,"method":"getTransaction",
  "params":["<signature>",{"commitment":"finalized","encoding":"jsonParsed","maxSupportedTransactionVersion":0}]
}'
```

Look at `meta.err` (it must be `null`), the signers in
`transaction.message.accountKeys`, and `meta.preTokenBalances` against
`meta.postTokenBalances`. A `shielded-exact` payment has no token balance
change.
