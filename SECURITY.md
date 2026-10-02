# Security

## Report a vulnerability

Report it privately through GitHub: open the **Security** tab of this
repository, then **Report a vulnerability**. To encrypt details, use our PGP
key `B136F0B026AF91667C56297903C2725212449869` (the Administrator key that
signs the announcements on our onion board).

Do not open a public issue for a vulnerability. Do not post it on our board
or on social media until we have fixed it or 90 days have passed, whichever
comes first.

Put in the report:

- what you found, and where (the wallet file version, or the URL);
- the steps to show it, with your own wallet and small amounts;
- what an attacker could do with it (move funds, link a payer to a payment,
  learn an amount, stop exits, and so on).

We read every report.

## In scope

- The wallet file `shinjuku-wallet.mjs`, every published version.
- The facilitator and relayer routes on `https://shinjukustaition.com` and
  its onion service.
- Privacy defects: anything that links a payer to a payment, or shows an
  amount, more than the README says.

## Out of scope

- Services that are not ours: RPC providers, Privacy Cash, exchanges,
  ChangeNOW, Tor.
- Denial of service, load tests, and rate-limit tests.
- Social engineering of our staff or our users.
- The limits that the README already lists under "What it does not hide".

## Safe harbour

You may read and inspect the wallet's code to check its security. If you
research in good faith, follow this policy, use only your
own wallets and funds, and do not touch other people's funds or data, we
will not take legal action against you for that research.

## Protect yourself

- Download the wallet only from `https://shinjukustaition.com/wallet/` or
  our onion service. We never send the wallet file by email or direct
  message.
- Check the SHA-256 before every first run (see the README). Compare the
  hash on our website with the hash in this repository.
- We never ask for your seed, passphrase, recovery file, or Solana secret
  key. Anyone who asks is not us.
- Keep your recovery file in two offline places. Run `backup-export` and
  `backup-verify` after every step that moves value.
- If your seed or recovery file leaks, stop using that wallet. Move your
  balance out with `unshield` to a new address that only you control.
