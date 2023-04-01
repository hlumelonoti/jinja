mport pandas as pd
import numpy as np
import talib

# Load forex data from CSV file
data = pd.read_csv("forex_data.csv")

# Compute moving averages
data['ma_5'] = talib.SMA(data['Close'], timeperiod=5)
data['ma_20'] = talib.SMA(data['Close'], timeperiod=20)

# Compute RSI
data['rsi'] = talib.RSI(data['Close'], timeperiod=14)

# Generate signals based on MA and RSI
signals = np.zeros(data.shape[0])
for i in range(1, data.shape[0]):
    if data['ma_5'][i] > data['ma_20'][i] and data['rsi'][i] < 30:
        signals[i] = 1
    elif data['ma_5'][i] < data['ma_20'][i] and data['rsi'][i] > 70:
        signals[i] = -1

# Compute positions based on signals
position = np.zeros(data.shape[0])
for i in range(1, data.shape[0]):
    if signals[i] == 1:
        position[i] = 1
    elif signals[i] == -1:
        position[i] = -1

# Compute returns based on positions and price changes
returns = position * (data['Close'].shift(-1) - data['Close'])

# Compute strategy returns based on signals and price changes
strategyReturns = signals * (data['Close'].shift(-1) - data['Close'])

# Compute cumulative returns
cumulativeReturns = np.cumsum(returns)

# Print results
print("Signals:\n", signals)
print("Position:\n", position)
print("Returns:\n", returns)
print("Strategy Returns:\n", strategyReturns)
print("Cumulative Returns:\n", cumulativeReturns)
