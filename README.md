import yfinance as yf
import datetime as dt

# Define the ticker symbol for Nifty 50 Futures
ticker_symbol = '^NSEI'

# Define the timeframes
previous_15min = dt.timedelta(minutes=15)
current_5min = dt.timedelta(minutes=5)

# Get today's date
today = dt.datetime.today().date()

# Define the start and end times for the analysis
end_time = dt.datetime.combine(today, dt.time(15, 30))  # Assuming market closes at 3:30 PM
start_time = end_time - current_5min

# Fetch Nifty 50 Futures data
nifty_data = yf.download(ticker_symbol, start=start_time, end=end_time, interval='5m')

# Calculate VWAP (Volume Weighted Average Price)
nifty_data['TP'] = (nifty_data['High'] + nifty_data['Low'] + nifty_data['Close']) / 3
nifty_data['TPV'] = nifty_data['TP'] * nifty_data['Volume']
vwap = nifty_data['TPV'].sum() / nifty_data['Volume'].sum()

# Filter data for the previous 15 minutes and the current 5 minutes
prev_15min_data = nifty_data[end_time - previous_15min:end_time]
current_5min_data = nifty_data[start_time:end_time]

# Calculate volumes
prev_15min_volume = prev_15min_data['Volume'].sum()
current_5min_volume = current_5min_data['Volume'].sum()

# Check conditions
if current_5min_volume > prev_15min_volume and current_5min_volume > vwap:
    print("Conditions met: Current 5-minute volume is greater than previous 15-minute volume and above VWAP.")
else:
    print("Conditions not met.")
