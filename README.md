# Exchange Simulator
[![Build Status](https://github.com/sergei-bondarenko/xchg/workflows/build/badge.svg?branch=master&event=push)](https://github.com/sergei-bondarenko/xchg/actions?query=workflow%3Abuild)
[![codecov](https://codecov.io/gh/sergei-bondarenko/xchg/branch/master/graph/badge.svg)](https://codecov.io/gh/sergei-bondarenko/xchg)
[![Python Version](https://img.shields.io/pypi/pyversions/xchg.svg)](https://pypi.org/project/xchg/)

Simulator of a currency exchange. Key features:
- Very minimalistic (300 lines in code), written in a functional style;
- You can download a sample data from poloniex.com or provide your data in .csv files;
- You can set an initial balance, use basic functions like buy, sell, go to the next time step;
- More advanced features like a total capital calculation, and make portfolio, more about that at the link below;
- Trading fees and minimum order amounts are supported.

**Full API documentation is here: https://sergei-bondarenko.github.io/xchg/**

## Quick start

The package is available on [pypi.org](https://pypi.org/project/xchg/), so you can install it via pip:
```bash
pip install xchg
```

Then we need a market data. For tutorial purposes you can download it with this command which will be available after package installation:
```bash
download_candles
```

It will download 50 candles for ETC, ETH, LTC and XMR cryptocurrencies from [poloniex.com](https://poloniex.com/) exchange and put it in `sample_data/` directory in the current path. Or you can download `sample_data/` directory from this repository.

Later you can view these .csv files and use your own data in the same format. You can have a different number of currecies, and different set of columns. The only mandatory column is "close" price, as it will used for trading. Just ensure that you have the same time range for different currencies and have no gaps in data.

Prices in .csv files are expressed in a base currency, which will be called __cash__ (if you are curious, in the sample data cash currency is BTC).

Now let's trade!

```python3
from xchg import Xchg

# Set an initial balance. Let's set that we have only cash currency at the
# start.
balance = {'cash': 100, 'ETC': 0, 'ETH': 0, 'LTC': 0}

# Set a trading fee which will be paid for each buy or sell trade (in this
# example it's 1%). You can set 0 if you don't want any fee to be paid.
fee = 0.01

# Set a minimum order size expressed in a cash currency. You can not place
# orders less than that value. You can use also set 0 here if you don't
# want to limit a minimum order size.
min_order_size = 0.001

# Create an exchange.
ex = Xchg(balance, fee, min_order_size, data_path='sample_data/')

# Let's buy some coins.
ex = ex.buy('ETC', 7500)
ex = ex.buy('LTC', 15000)

print(ex.balance)
# Output: {'cash': 1.2146212121212017, 'ETC': 7500, 'ETH': 0, 'LTC': 15000}

# All done, now we want to wait for 30 time periods.
for _ in range(30):
  ex = ex.next_step()

# Now we want to sell.
ex = ex.sell('ETC', 7500)
ex = ex.sell('LTC', 15000)

print(ex.balance)
# Output: {'cash': 101.1345539621212, 'ETC': 0, 'ETH': 0, 'LTC': 0}

# We made 1 BTC, yay!
```

## For developers
Use the following command to running tests locally:
```python3
pytest --flake8 --cov=xchg --cov-report term-missing -vvv
```
And do not forget to increase a version in `xchg/__init__.py` before commiting.
