### FAANG Stock Min-Max (Part 1) [Bloomberg SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/sql-bloomberg-stock-min-max-1">Read the original question page</a>

The Bloomberg terminal is the go-to resource for financial professionals, offering convenient access to a wide array of financial datasets. As a Data Analyst at Bloomberg, you have access to historical data on stock performance.

Currently, you're analyzing the highest and lowest open prices for each FAANG stock by month over the years.

For each FAANG stock, display the ticker symbol, the month and year ('Mon-YYYY') with the corresponding highest and lowest open prices (refer to the Example Output format). Ensure that the results are sorted by ticker symbol.



`stock_prices` **Table**:

| **Column Name** | **Type** |                           **Description**                           |
|:---------------:|:--------:|:-------------------------------------------------------------------:|
| date            | datetime | The specified date (mm/dd/yyyy) of the stock data.                  |
| ticker          | varchar  | The stock ticker symbol (e.g., AAPL) for the corresponding company. |
| open            | decimal  | The opening price of the stock at the start of the trading day.     |
| high            | decimal  | The highest price reached by the stock during the trading day.      |
| low             | decimal  | The lowest price reached by the stock during the trading day.       |
| close           | decimal  | The closing price of the stock at the end of the trading day.       |

`stock_prices` **Example Input**:

Note that the table below displays randomly selected AAPL data.

| **date**            | **ticker** | **open** | **high** | **low** | **close** |
|---------------------|------------|----------|----------|---------|-----------|
| 01/31/2023 00:00:00 | AAPL       | 142.28   | 142.70   | 144.34  | 144.29    |
| 02/28/2023 00:00:00 | AAPL       | 146.83   | 147.05   | 149.08  | 147.41    |
| 03/31/2023 00:00:00 | AAPL       | 161.91   | 162.44   | 165.00  | 164.90    |
| 04/30/2023 00:00:00 | AAPL       | 167.88   | 168.49   | 169.85  | 169.68    |
| 05/31/2023 00:00:00 | AAPL       | 176.76   | 177.33   | 179.35  | 177.25    |

**Example Output**:

| **ticker** | **highest_mth** | **highest_open** | **lowest_mth** | **lowest_open** |
|------------|-----------------|------------------|----------------|-----------------|
| AAPL       | May-2023        | 176.76           | Jan-2023       | 142.28          |

**Explanation**

Apple Inc. (AAPL) achieved its highest opening price of $176.76 in May 2023 and its lowest opening price of $142.28 in January 2023.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH query AS
(
SELECT ticker, 
  TO_CHAR(date,'Mon-YYYY') AS mth,
  open,
  RANK() OVER(PARTITION BY ticker ORDER BY open DESC) AS rankdesc,
  RANK() OVER(PARTITION BY ticker ORDER BY open) AS rankasc
FROM stock_prices
)
SELECT qh.ticker,
  qh.mth AS highest_mth,
  qh.open AS highest_open,
  ql.mth AS lowest_mth,
  ql.open AS lowest_open
FROM query AS qh
INNER JOIN query AS ql
ON qh.ticker = ql.ticker
AND qh.rankdesc = ql.rankasc
WHERE qh.rankdesc =1;
```


**Solution Output**

| **ticker** | **highest_mth** | **highest_open** | **lowest_mth** | **lowest_open** |
|:----------:|:---------------:|:----------------:|:--------------:|:---------------:|
| AAPL       | Jul-2023        | 195.26           | Apr-2020       | 61.63           |
| AMZN       | Dec-2021        | 177.25           | Jan-2023       | 85.46           |
| GOOG       | Nov-2021        | 148.16           | Apr-2020       | 56.10           |
| META       | Sep-2021        | 379.59           | Nov-2022       | 94.33           |
| MSFT       | Jul-2023        | 339.19           | Apr-2020       | 153.00          |
| NFLX       | Nov-2021        | 689.06           | Jul-2022       | 176.49          |
