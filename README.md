# **BitMint Protocol**

### *Turn Idle Bitcoin into Productive Capital*

---

## **Overview**

**BitMint** is a decentralized lending protocol built on the **Stacks** blockchain, designed to unlock the liquidity of Bitcoin without sacrificing custody or exposure.
By leveraging **overcollateralized BTC-backed loans**, users can mint synthetic stablecoins while maintaining full BTC upside.
All transactions and settlements are anchored to Bitcoin’s immutable security via Stacks' native Bitcoin finality.

---

## **System Summary**

* **Purpose:**
  Enable Bitcoin holders to borrow against their holdings, turning idle BTC into productive capital.

* **Core Value Proposition:**
  Users retain full exposure to Bitcoin while accessing on-demand liquidity for trading, payments, or yield strategies.

* **Settlement Security:**
  Trustless operations are enforced through Clarity smart contracts on Stacks, inheriting Bitcoin’s final settlement guarantees.

---

## **Key Features**

| Feature                          | Description                                                                                               |
| -------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **BTC-Backed Collateralization** | Lock Bitcoin-based assets to mint synthetic stablecoins while maintaining BTC exposure.                   |
| **Overcollateralized Loans**     | Loans are issued based on minimum collateral ratios and liquidation thresholds to ensure solvency.        |
| **Dynamic Risk Parameters**      | Configurable collateral ratios, liquidation thresholds, and fee rates adjustable by protocol governance.  |
| **Transparent Price Oracle**     | Trusted on-chain feed for real-time BTC price updates used in collateral valuation and liquidation logic. |
| **Non-Custodial Settlement**     | Users maintain self-custody; protocol logic enforces liquidation and repayment autonomously.              |
| **Data Visibility**              | Full on-chain transparency for collateral, loan status, and system metrics.                               |

---

## **System Architecture**

```
+-------------------------------------------------------------+
|                         BitMint Protocol                    |
|-------------------------------------------------------------|
|                    Clarity Smart Contract Layer              |
|                                                             |
|  +----------------------+       +------------------------+  |
|  |   Collateral Module  | <---> |   Loan Management      |  |
|  |  - BTC deposits      |       |  - Loan creation       |  |
|  |  - Collateral ratio  |       |  - Repayment tracking  |  |
|  |  - Liquidation check |       |  - Interest accrual    |  |
|  +----------------------+       +------------------------+  |
|                                                             |
|  +----------------------+       +------------------------+  |
|  |  Oracle & Admin Ops  |       |   User Loan Index      |  |
|  |  - BTC price feed    |       |  - Track active loans  |  |
|  |  - Protocol configs  |       |  - Loan ownership map  |  |
|  +----------------------+       +------------------------+  |
|                                                             |
+-------------------------------------------------------------+
                   ↓                       ↓
        Bitcoin Settlement Layer     Stacks Blockchain State
```

---

## **Contract Architecture**

### **1. Configuration Layer**

Holds system-wide parameters and ensures proper initialization.

* **Variables:**

  * `platform-initialized` — marks activation state
  * `minimum-collateral-ratio` — base ratio (e.g., 150%)
  * `liquidation-threshold` — triggers forced liquidation (e.g., 120%)
  * `platform-fee-rate` — service fee applied to loans

### **2. Collateral Management**

Tracks all BTC collateral locked by users.

* **Functions:**

  * `deposit-collateral` — records collateral deposits
  * `update-price-feed` — updates BTC price via admin oracle feed

### **3. Loan Management**

Handles creation, tracking, and repayment of loans.

* **Functions:**

  * `request-loan` — open new BTC-backed loan
  * `repay-loan` — repay borrowed amount plus accrued interest
  * `check-liquidation` — verifies if a position should be liquidated

### **4. Data Structures**

| Map / Var                                     | Purpose                             |
| --------------------------------------------- | ----------------------------------- |
| **`loans`**                                   | Registry of all loan records        |
| **`user-loans`**                              | Tracks active loan IDs per user     |
| **`collateral-prices`**                       | Maintains oracle-fed BTC price data |
| **`total-btc-locked` / `total-loans-issued`** | Global protocol metrics             |

### **5. Error Handling**

All operations use explicit error codes (`u100+`) for deterministic behavior and testability.
Example:

* `ERR-NOT-AUTHORIZED (u100)`
* `ERR-INSUFFICIENT-COLLATERAL (u101)`
* `ERR-INVALID-AMOUNT (u103)`

---

## **Data Flow Overview**

```
[ User deposits BTC ] 
        │
        ▼
[ Collateral recorded in protocol ]
        │
        ▼
[ Loan request validated ]
        │   ├─> Check BTC price
        │   ├─> Compute required collateral ratio
        │   └─> Assign loan ID
        ▼
[ Loan issued + synthetic stablecoin minted ]
        │
        ▼
[ User repays loan + interest ]
        │
        └─> Collateral released
```

If collateral ratio falls **below liquidation threshold**, the system automatically executes:

```
→ Loan flagged as liquidated
→ Collateral seized
→ Loan marked inactive
```

---

## **Read-Only Functions**

| Function                    | Description                                  |
| --------------------------- | -------------------------------------------- |
| `get-loan-details(loan-id)` | Returns detailed loan record                 |
| `get-user-loans(user)`      | Fetches all active loan IDs for a given user |
| `get-platform-stats()`      | Displays key system metrics                  |
| `get-valid-assets()`        | Returns whitelisted collateral assets        |

---

## **Administrative Controls**

| Function                                      | Description                                          |
| --------------------------------------------- | ---------------------------------------------------- |
| `initialize-platform()`                       | One-time setup; can only be called by contract owner |
| `update-collateral-ratio(new-ratio)`          | Adjust global collateral requirements                |
| `update-liquidation-threshold(new-threshold)` | Modify liquidation sensitivity                       |
| `update-price-feed(asset, new-price)`         | Oracle feed management                               |

---

## **Security & Design Principles**

* **Deterministic Execution:**
  All protocol logic is transparent and predictable — no hidden states or off-chain dependencies.

* **No Custodial Risk:**
  Users’ BTC exposure is never rehypothecated; collateral is secured by the protocol contract.

* **Governance-Ready:**
  Parameter updates are restricted to the contract owner (or future DAO governance).

* **Bitcoin Finality:**
  All state transitions are anchored to Bitcoin through Stacks’ consensus model.

---

## **Future Extensions**

* Integration with decentralized price oracles (e.g., Hiro Oracles, Chainlink on Stacks).
* Support for multi-asset collateralization beyond BTC and STX.
* Dynamic interest rate model based on utilization ratios.
* Cross-protocol composability with stablecoin liquidity pools.

---

## **Deployment**

| Environment        | Status     | Notes                             |
| ------------------ | ---------- | --------------------------------- |
| **Stacks Testnet** | ✅ Active   | For testing protocol logic        |
| **Stacks Mainnet** | 🔒 Pending | Deployment after governance audit |

---

## **License**

This project is open-sourced under the **MIT License**.
Use freely, contribute responsibly, and maintain transparency aligned with Bitcoin’s ethos.
