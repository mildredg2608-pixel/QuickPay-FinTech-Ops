# Spreadsheet Answers

## Cleaning Steps
- Standardized all `merchant_name` entries by removing extra spaces and correcting case (e.g., "alpha mart" to "Alpha Mart").
- Converted `transaction_date` to standard YYYY-MM-DD format.
- Handled `risk_score` by removing text prefixes like "score:" and "risk-" to leave only numerical values.

## Standardization Rules
- Status: Unified various failed messages into standard "FAILED" or "CHARGEBACK".
- Region: Normalized gateway regions to uppercase (APAC, EU, US).

## Lookup and Enrichment Logic
- Calculated `amount_usd` as as per formula mentioned below.
- Used `VLOOKUP` against `Merchants.csv` to map the `Merchant id` and `region`.

## Final Answers
- Total raw rows: 30
- Total cleaned rows: 30
- Invalid or missing rows handled: 10 (9 transactions had missing `gateway_region` values that were successfully enriched using the merchant master lookup, and 1 transaction had a missing `risk_score` value which was defaulted to safe/0 handling).
- Top region by GMV: APAC ($82,229.00)
- Number of high value transactions: 7
- Number of high risk transactions: 9
- Top merchant by captured GMV: Beta Stores ($33,141.50)

## Formula Samples
- USD Conversion: =D2*VLOOKUP(E2,Rates!$B$1:$C$19,2,FALSE)
- High Risk Flag: =IF(OR(G2>=70, F2="CHARGEBACK"), 1, 0)
- High Value Transaction Flag: =IF(OR(AND(L:L="APAC", K:K> 5000), AND(L:L="EU", K:K> 6000), AND(L:L="US", K:K> 7000)), 1, 0)