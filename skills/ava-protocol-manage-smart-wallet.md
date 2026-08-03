---
name: Manage an Ava Protocol smart wallet
description: Derive, inspect, and withdraw from a non-custodial smart wallet.
api: openapi/ava-protocol-avs-openapi-original.yml
operations: [listWallets, createWallet, getWalletNonce, updateWallet, withdrawWallet]
---

# Manage a smart wallet

Ava Protocol executes automations from **smart wallets** — segmented accounts with scoped
permissions, derived deterministically (CREATE2) from `(owner, salt, factory)`. Authenticate first.

## Steps

1. **List** existing wallets — **listWallets** (`GET /wallets`).
2. **Create / ensure** a wallet — **createWallet** (`POST /wallets`) with `{ salt, factoryAddress? }`.
   This is an idempotent "ensure exists": it returns the deterministic derived address, so it is
   safe to retry.
3. **Check nonce** before submitting actions — **getWalletNonce** (`GET /wallets/{address}:getNonce`).
4. **Update** wallet properties (e.g. hide) — **updateWallet** (`PATCH /wallets/{address}`).
5. **Withdraw** funds — **withdrawWallet** (`POST /wallets/{address}:withdraw`). This is a
   fund-moving, safety-critical action: confirm intent explicitly before calling.

## Rules

- Funds are non-custodial; the user retains control. Withdrawals and transfers should require
  explicit human approval (see agentic-access/ava-protocol-agentic-access.yml).
- Errors are RFC 7807; `code` is stable (see errors/ava-protocol-problem-types.yml).
