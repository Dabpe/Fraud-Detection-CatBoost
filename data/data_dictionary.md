# Data dictionary — Bank card fraud detection

100,000 synthetic card transactions from 7,000 customers over 60 days
(2026-01-01 to 2026-03-01). Amounts are in USD.

| Column | Type | Description |
| --- | --- | --- |
| `transaction_id` | string | Unique transaction id. Assigned in chronological order; carries no signal. |
| `timestamp` | datetime | Transaction time, `YYYY-MM-DD HH:MM:SS`. |
| `card_id` | string | Customer card. A card appears in many transactions; join on this to build behavioural features. |
| `card_holder` | string | Synthetic cardholder name (Faker). Decorative, not predictive. |
| `customer_age` | int | Cardholder age, 18–84. |
| `customer_home_country` | string | ISO 3166-1 alpha-2 country of the cardholder. |
| `account_age_days` | int | Age of the account in days. |
| `amount` | float | Transaction amount in USD, 2 decimals. |
| `merchant_id` | string | Merchant identifier. |
| `merchant_name` | string | Synthetic merchant name (Faker). |
| `merchant_category` | string | One of: `grocery`, `restaurant`, `fuel`, `retail`, `electronics`, `travel`, `digital`, `gambling`, `crypto`, `pharmacy`, `utilities`, `entertainment`. |
| `merchant_country` | string | ISO country where the merchant is registered. |
| `is_foreign_merchant` | 0/1 | `1` if `merchant_country != customer_home_country`. |
| `channel` | string | `online`, `in_store` or `recurring`. |
| `card_present` | bool | `True` only for in-store transactions. |
| `device_id` | string | Device used for online transactions. **Blank** for in-store and recurring. |
| `ip_address` | string | Anonymised IPv4 address. Not geolocatable — use `ip_country`. |
| `ip_country` | string | Country the request came from. |
| `is_foreign_ip` | 0/1 | `1` if `ip_country != customer_home_country`. |
| `is_fraud` | 0/1 | **Training set only.** Target label. Noisy by design: some fraud is never reported, so a few fraudulent rows are labelled 0. |

## Notes for competitors

- **Class imbalance:** about 1.4% of transactions are labelled fraudulent. Accuracy
  is not a useful metric; the competition is scored on ROC AUC.
- **Noisy labels:** roughly 10% of the fraud in the data is never reported and is
  labelled legitimate. Do not expect (or chase) an AUC near 1.0; a score around
  0.93 is already excellent.
- **Train/test period:** `train.csv` covers days 1–36 and `test.csv` covers days
  37–60. The test rows are fully available at prediction time (no future
  information is hidden), so aggregate features computed over `train + test` are
  allowed as long as no labels are used.
- **Cards repeat across the split:** most test cards also appear in training.
  Behavioural aggregates per `card_id` are therefore very useful.
- **Missingness is meaningful:** `device_id` being blank simply means the
  transaction happened without a device (in-store terminal or recurring billing).
- **Currency:** all amounts are already in USD.
- **Country codes:** ISO 3166-1 alpha-2 (`US`, `GB`, `DE`, …). 18 countries appear.
