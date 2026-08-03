---
name: Authenticate to the Ava Protocol AVS API
description: Exchange a wallet signature for a JWT bearer token and attach it to every request.
api: openapi/ava-protocol-avs-openapi-original.yml
operations: [authExchange]
---

# Authenticate to Ava Protocol

Ava Protocol's AVS aggregator uses a single credential: a **JWT bearer token** sent as
`Authorization: Bearer <jwt>` on every request. Base URL: `https://gateway.avaprotocol.org/api/v1`.

## Steps

1. Read the aggregator version (optional but recommended) — `GET /health`. The `version`
   field is used to stamp the canonical EIP-191 auth message.
2. Build the canonical EIP-191 message client-side and have the user's wallet sign it
   (wagmi / viem / `window.ethereum`).
3. Call **authExchange** — `POST /auth:exchange` with `{ message, signature, ownerAddress }`.
   The response contains the JWT bearer token.
4. Set the token once on the client; attach `Authorization: Bearer <jwt>` to every subsequent call.

## Rules

- Browser-signed JWTs expire after **24 hours**. On `401` with code `AUTH_REQUIRED`, do **not**
  retry with the stale token — re-run `authExchange` and retry once.
- For server-to-server / background jobs, use an operator-minted long-lived key from the
  `ap create-api-key` CLI instead of the wallet flow (see cli/ava-protocol-cli.yml).
- Errors are RFC 7807 `application/problem+json`; switch on the `code` field
  (see errors/ava-protocol-problem-types.yml).
