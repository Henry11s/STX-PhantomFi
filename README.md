
# STX-PhantomFi-

A Clarity-based smart contract enabling users to mint, manage, and transfer synthetic assets backed by STX collateral. The system ensures over-collateralization, uses an external price oracle for valuation, and includes mechanisms for liquidation and supply control.

---

## 🚀 Features

* ✅ Mint synthetic tokens by depositing STX as collateral
* 🔁 Transfer tokens between users
* 🔄 Burn tokens and unlock collateral
* 🛡️ Enforced overcollateralization with safety and liquidation ratios
* 📉 Oracle-based real-time pricing
* ⚖️ Liquidation of undercollateralized positions
* 🧮 Overflow-safe arithmetic
* 📦 Fully on-chain state and access control

---

## ⚙️ System Constants

| Constant                              | Description                                       |
| ------------------------------------- | ------------------------------------------------- |
| `COLLATERAL-SAFETY-RATIO = 150%`      | Minimum collateralization required for minting    |
| `COLLATERAL-LIQUIDATION-RATIO = 120%` | Threshold below which positions can be liquidated |
| `PRICE-VALIDITY-PERIOD-BLOCKS = 900`  | Maximum age (in blocks) for the oracle price      |
| `MIN-TOKEN-ISSUANCE-AMOUNT = 1.00`    | Minimum token minting amount                      |
| `MAX-ALLOWED-PRICE`                   | Cap on oracle price to avoid manipulation         |

---

## 🛠 Deployment

### Prerequisites

* [Clarity language](https://docs.stacks.co/write-smart-contracts/overview)
* [Clarinet](https://github.com/hirosystems/clarinet) (for local development and testing)

### Deploy

```bash
clarinet check
clarinet deploy
```

---

## 📦 Contract Architecture

### Data Structures

* `user-token-holdings`: Mapping of users to their synthetic token balances
* `collateralized-positions`: Mapping of users to their positions (`collateral`, `issued tokens`, `entry price`)
* Global state:

  * `global-token-supply`
  * `current-market-price`
  * `oracle-update-block-height`

---

## 🔐 Access Control

* Only `SYSTEM-ADMIN` (deployer) can update the price feed.
* Liquidations can be initiated by any user when a position falls below the liquidation ratio.

---

## ⚙️ Public Functions

### 🔁 Mint & Burn

* `create-synthetic-tokens(token-amount)`: Mints synthetic tokens with collateral
* `destroy-synthetic-tokens(token-amount)`: Burns tokens and releases proportional collateral

### 💸 Transfer

* `send-synthetic-tokens(recipient, amount)`: Transfers tokens between users

### 💰 Collateral Management

* `add-collateral(collateral-amount)`: Top up existing collateral
* `force-close-position(position-owner)`: Liquidate unsafe positions

### 📈 Oracle & Supply

* `set-price-feed-value(new-price)`: Admin-only function to set market price
* `get-market-price()`: Returns current price from the oracle
* `get-circulating-supply()`: Total tokens in circulation

### 📊 Read-Only Utilities

* `get-user-token-balance(account)`: Token balance for a user
* `get-position-details(owner)`: Position details for a user
* `calculate-position-health-ratio(owner)`: Collateral health percentage

---

## ❌ Error Codes

| Code       | Description                |
| ---------- | -------------------------- |
| `err u100` | Not authorized             |
| `err u101` | Token balance too low      |
| `err u102` | Invalid token quantity     |
| `err u103` | Oracle data is stale       |
| `err u104` | Insufficient collateral    |
| `err u105` | Collateral below threshold |
| `err u106` | Price out of bounds        |
| `err u107` | Overflow in calculation    |
| `err u108` | Invalid transfer recipient |
| `err u109` | Amount must be positive    |
| `err u110` | Vault not found            |

---

## 📚 Example Workflow

1. **User mints tokens:**

```clojure
(create-synthetic-tokens u100000000) ;; 1.00 token
```

2. **Transfers token to another user:**

```clojure
(send-synthetic-tokens 'SPXYZ... u50000000) ;; 0.5 tokens
```

3. **Burn tokens and withdraw STX:**

```clojure
(destroy-synthetic-tokens u100000000)
```

4. **Force-close undercollateralized position:**

```clojure
(force-close-position 'SPABC...)
```

---
