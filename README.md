# [Hyperbridge](https://hyperbridge.network/) Integration Guide

## 📘 Overview

This guide walks through the steps required to integrate **Hyperbridge** for cross-chain asset transfers between:

- Substrate
- EVM (e.g. Sepolia)

---

## 🧱 Prerequisites

- Access to:
  - Substrate chain (with `tokenGateway` pallet)
  - Sepolia Ethereum RPC
- Installed relayers
- Testnet funds:
  - Native token
  - `USD.h` (fee token)

---

## ⚙️ Step-by-Step Integration

### 1. Start Consensus Relayer

- Follow: **Setting up consensus relayer**
- Share the consensus state with the Hyperbridge team
- It must be **whitelisted** before starting the relayer

> ⚠️ Note:
> Kusama / Polkadot consensus states must also be whitelisted on your chain

---

### 2. Start Messaging Relayer

- Follow: **Setting up messaging relayer**

---

### 3. Create ERC6160 Asset (Substrate)

- **Pallet:** `tokenGateway`
- **Extrinsic:** `createErc6160Asset`

---

### 4. Deploy Hyperfungible Token (Sepolia)

- Use **Hyper Fungible Token Contract**
- Choose any:
  - Token name
  - Token symbol
- Share the contract address with the Hyperbridge team

---

### 5. Set Token Gateway Address

- **Pallet:** `tokenGateway`
- **Extrinsic:** `setTokenGatewayAddresses`

Sepolia Token Gateway: 0xFcDa26cA021d5535C3059547390E6cCd8De7acA6

---

### 6. Grant Roles

Grant the following roles to the Token Gateway:

- `MINTER_ROLE`
- `BURNER_ROLE`

---

### 7. Approvals

Approve:

- Token Gateway contract
- Fee Token contract

---

### 8. Fund Wallet

You need `USD.h` tokens for transaction fees.

Get from: **Testnet Fee Token Faucet**

---

## 🔄 Token Transfers

### ➡️ Substrate → Sepolia

- **Pallet:** `tokenGateway`
- **Extrinsic:** `teleport`

---

### ⬅️ Sepolia → Substrate

- Use `teleport` function from Token Gateway (EVM)
- Create a script with correct parameters

---

## 🔍 Verification

- Check explorer logs for successful asset import

---

## ✅ Setup Checklist

- [ ] ERC6160 token created on Substrate
- [ ] Token deployed on Sepolia (same name & symbol)
- [ ] `MINTER_ROLE` granted
- [ ] `BURNER_ROLE` granted
- [ ] Token Gateway approved
- [ ] Fee token approved

---

## 🔍 Observations

### Native Token Behavior

- Asset `0` is treated as the native token by Hyperbridge
- Transfers from Sepolia updated native token balance

---

### Asset Initialization Requirement

- `pallet-assets` must:
  - Create the asset
  - Mint tokens

Required for Token Gateway to recognize assets for teleport

---

## ⚠️ Notes

- Ensure relayers are running
- Ensure sufficient fees
- Ensure roles are properly assigned

[Request Flow](docs/test.md)
