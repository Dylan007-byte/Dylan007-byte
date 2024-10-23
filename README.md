-import backtrader as bt

class XAUUSDBot(bt.Strategy):
    params = (('fast_ma', 10), ('slow_ma', 30))

    def __init__(self):
        self.fast_ma = bt.ind.SMA(period=self.params.fast_ma)
        self.slow_ma = bt.ind.SMA(period=self.params.slow_ma)
        self.crossover = bt.ind.CrossOver(self.fast_ma, self.slow_ma)

    def next(self):
        if self.crossover > 0:
            self.buy()
        elif self.crossover < 0:
            self.sell()

cerebro = bt.Cerebro()
cerebro.addstrategy(XAUUSDBot)
cerebro.run()
import ccxt

# Create a CCXT exchange instance
exchange = ccxt.binance()

# Create a data feed
data_feed = bt.feeds.CCXT(exchange, 'XAUUSD', timeframe=bt.TimeFrame.Minutes)

# Add the data feed to the cerebro engine
cerebro.adddata(data_feed)

# Set the broker
cerebro.broker.setcash(10000.0)

# Run the backtest
cerebro.run()
cerebro.optstrategy(XAUUSDBot, fast_ma=[10, 20, 30], slow_ma=[30, 60, 90])
