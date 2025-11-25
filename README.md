# Task-4-stock-intraday
This script downloads 1-minute stock data using yfinance for a given date range, then performs an intraday backtest. It buys at a set entry time, sells at an exit time, calculates daily P/L, totals the profit/loss, and saves the results to a CSV file.

# STEP  

(FIRST STEP): Install & Import Required Libraries

Before running the code, make sure you install the required library:
pip install yfinance pandas


(Then) in Python, import the necessary modules:

import yfinance as yf
import pandas as pd
from datetime import datetime, timedelta, time

# HOW THE CODE WORKS 

I’ll explain in the exact sequence your script runs.
**STEP 1 — Take User Inputs**
You define:
Date range
Stock name
Entry (buy) time
Exit (sell) time
Which price to consider (open / close)
Qty of shares
Output CSV name

Example:

START_DATE = "2025-11-14"
END_DATE   = "2025-11-19"
TICKER     = "SBIN.NS"
ENTRY_TIME = "09:30"
EXIT_TIME  = "15:00"
PRICE_ON   = "close"
QTY        = 1

**STEP 2 — Convert Time Strings to Time Objects**
entry_t = parse_time(ENTRY_TIME)
exit_t  = parse_time(EXIT_TIME)


Now "09:30" becomes a Python time(9,30).

**STEP 3 — Prepare Dates for yfinance Download**

yfinance does NOT include the last date unless you add +1 day.

start = pd.to_datetime(START_DATE)
end   = pd.to_datetime(END_DATE) + pd.Timedelta(days=1)

**STEP 4 — Download 1-Minute Data Using yfinance**
df = yf.download(TICKER, start=..., end=..., interval="1m")


If no data is returned → you print warning & exit.

**STEP 5 — Clean the DataFrame**

Rename columns:

df = df.rename(columns={"Open":"open","Close":"close"})


Add a date column:

df["date"] = df.index.date


Remove timezone from index:

df.index = df.index.tz_localize(None)

**STEP 6 — Define Function to Get Price at a Specific Time**

When you request a time like 09:30:

If exact time exists → return price

If not, find next minute

If nothing later → use last available

def get_price(rows, ts, field):

**STEP 7 — Loop Through Every Date in the Range**
current = start.date()
while current <= end_date:

For each day:
Extract today's data
If no rows → append "no data"
Else → calculate entry & exit price

**STEP 8 — Build Result for Each Day**

Entry timestamp:
entry_ts = datetime.combine(current, entry_t)

Exit timestamp:
exit_ts  = datetime.combine(current, exit_t)

Calculate:
entry_price
exit_price
pnl_per_share
pnl_total
Add to result list.

**STEP 9 — Convert Results to DataFrame**

res = pd.DataFrame(results, columns=[ ... ])

Print on screen:
print(res.to_string(index=False))

Print total P/L:
print(res['pnl_total'].dropna().sum())

STEP 10 — Save to CSV File
res.to_csv(OUTPUT_CSV, index=False)
