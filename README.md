# credit_score_model

## 🔍 Overview

This project builds a machine learning-based credit scoring system for wallets interacting with the Aave V2 protocol on the Polygon network. Each wallet is scored between **0 and 1000** based on its historical DeFi behavior.

High scores reflect responsible usage (e.g. repayments, long-term engagement), while low scores indicate risky or bot-like patterns (e.g. liquidations, short activity spans).

## 📁 Dataset

- Format: JSON
- File: `user-wallet-transactions.json`
- Records: ~100K user-level transaction logs
- Key actions: `deposit`, `borrow`, `repay`, `redeemunderlying`, `liquidationcall`

## 🧠 Features Engineered

From the raw transaction data, we computed:

| Feature | Description |
|--------|-------------|
| `deposit_count` | Number of deposits |
| `borrow_count` | Number of borrows |
| `repay_count` | Number of repayments |
| `redeem_count` | Withdrawals |
| `liquidation_count` | Times user got liquidated |
| `duration_days` | Time between first and last action |
| `repay_ratio` | `repay_count / borrow_count` |
| `total_actions` | Total activity count |

## 🧮 Scoring Logic

A weighted formula is applied:

```python
score = (
    0.3 * repay_ratio +
    0.3 * activity_score +   # normalized total_actions
    0.2 * longevity +        # normalized duration
    0.2 * (1 - risk_factor)  # penalize liquidations
)
credit_score = (score / max(score)) * 1000
