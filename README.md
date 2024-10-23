import yfinance as yf
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import classification_report

# Step 1: Fetch Historical Data
data = yf.download('XAUUSD=X', start='2020-01-01', end='2022-02-26')

# Step 2: Feature Engineering
data['Price_Change'] = data['Close'].shift(-1) - data['Close']
data['Signal'] = 0
data['Signal'][data['Price_Change'] > 0] = 1  # Buy signal
data['Signal'][data['Price_Change'] < 0] = -1  # Sell signal
data.dropna(inplace=True)

# Features and Target Variable
X = data[['Open', 'High', 'Low', 'Close', 'Volume']]
y = data['Signal']

# Step 3: Train-Test Split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Step 4: Train the Model
model = DecisionTreeClassifier()
model.fit(X_train, y_train)

# Step 5: Make Predictions
predictions = model.predict(X_test)

# Step 6: Evaluate the Model
print(classification_report(y_test, predictions))

# Step 7: Add Predictions to DataFrame
data['Predicted_Signal'] = 0
data.loc[X_test.index, 'Predicted_Signal'] = predictions

# Display the last few rows with predictions
print(data[['Close', 'Signal', 'Predicted_Signal']].tail())
