# Repository content

This repository contains scripts simulating trading of crypto currency coins.

NOTE: These scripts are proofs of concepts. They are not ready to be used for a
real trading.

# Algorithm based on broken supports

## Summary

The Jupyter Notebook file `broken_supports_algorithm.ipynb` contains a
a script simulating an algorithm based on broken supports. The
algorithm should buy cheap when other investors are in panic and hence they
are selling and the algorithm should sell higher, but still below the broken
support.

The motivation to write this script was one of the videos of Quickfingers Luc
(such as https://www.youtube.com/watch?v=vgcFe_XO_LQ).

## Environment preparation

Necessary python3 packages:
- jupyter
- pandas
- PyFunctional

You can install them via

`python3 -m pip install --user jupyter pandas PyFunctional`

## Execution

1. Execute `jupyter notebook`
1. Open `broken_supports_algorithm.ipynb` in the started Jupyter Notebook
editor.
1. Click on "Run" for each code snippet, starting with the first code snippet
and following with the second one, third one, etc.

NOTE 1: The last code snippet triggers the simulation; either
`currency_pair_evaluator.evaluate_one_time_frame_and_draw_and_close(..)` or
`currency_pair_evaluator.evaluate_multiple_time_frames_and_draw_and_close(..)`.

NOTE 2: This script is not used for a real trading, hence no secrets are
necessary.

## Dictionary

From https://en.wikipedia.org/wiki/Support_and_resistance :

> In stock market technical analysis, **support** and **resistance** are certain
predetermined levels of the price of a **security** at which it is thought that
the price will tend to stop and reverse. These levels are denoted by multiple
touches of price without a breakthrough of the level.

The **base** is either support or resistance. Note a support may become
a resistance and vice versa.

The specification when a support / resistance is strong and when it is not is
left for later (see TODO).

Assume that a strong support/resistance is being broken
the first time. The graph of the coin is **healthy** if the price comes back
to the base price in a close future and this happens for every strong base
. Note the price can start falling/rising again just after the price has come
back to the broken support/resistance.

The **base currency** is the currency kept long term due to limited volatility
and high liquidity. The **market (=target) currency** is the currency which
ups and downs expressed in the base currency wanted to be leveraged to
achieve profit. The **currency pair** BTC-USDT consists of the market
currency BTC and base currency USDT.

## Algorithm

### Theory of the algorithm

When a strong support is being broken in the health graph the first time,
the script waits till the price has fallen enough and then starts buying.
If the price continues falling, the script continues buying, but more than
before. Since the script operates on the health graph, the price needs to
come back to the broken support in the close future. The script sells all
coins even below the base price to reduce risk caused by other bots.

### Challenges of the algorithm

The theory contains expressions which needs to precisely specified:
 
1. How to identify a base price?
1. What means that a base is strong?
1. How to identify that a base has been broken?
1. When should the script start buying?
1. How much should the script buy?
1. When should the script sell coins?
1. How long can the comeback to the price take?
1. What should the script do when the comeback seems to not come?

### Implementation of the algorithm

#### How to identify a base price?

Humans generally prefers round numbers and may tend to use them as bounderies
in their limit orders. Bots operating on the exchanges may on the other side
follow large variety of different strategies. Me and my friend Preslav Rachev
(his LinkedIn profile https://www.linkedin.com/in/preslavrachev/) came up with
an idea to divide the chart into price bins //TODO1

 to create price bins (=zones) and I implemented The base price is typically a round number. However, if the arbitrage is possible what a cryptocurrency exchanges are
We cannot rely on the precise price. It may be that the a support has not been broken, even though the price has fallen slightly below the support price



#### What means that a base is strong?

//TODO analyze the script

//TODO continue with other questions

//TODO Picture of graph with supports, buys, sells

### Evaluation of the algorithm

//TODO Picture of finding the optimum

## Implementation details

### How to configure training data and data used in simulation

The training data directly precedes the data used in simulation. Both
the length of training and the length of simulation are configurable in the
last code snippet and their units are number of weeks. The former is hold in
the variable `old_week_count`, the latter (also called `new_week_count`) is
hold in the first item of the input pair of
`currency_pair_evaluator.evaluate_one_time_frame_and_draw_and_close(..)` or
`currency_pair_evaluator.evaluate_multiple_time_frames_and_draw_and_close
(..)`. The second item of the input pair of the two aforementioned functions
is an offset of both the training data and data for simulation:

Training data:
```
<NOW - old_week_count - new_week_count - offset, NOW - new_week_count - offset>
```

Data used in simulation:
```
<NOW - new_week_count - offset, NOW - offset>
```

Data are downloaded from Poloniex exchange using
`https://poloniex.com/public?command=returnChartData`. The training data and
data used in simulation are downloaded in two requests.

### Further algorithm configuration

In the `__init__` function of the `AlgorithmConfig` class:

- `initial_base_currency_amount` represents the initial budget for the
simulation. In the base currency.
- `max_buy_order_volume_in_wallet_percents` represents the upper bound of
volume in the market currency which can be bought at once. The
unit is the number of percents of the total budget in the wallet.
- `min_base_score` represents the lower bound of score when price bin is
identified as a base strong enough. //TODO2 how is the score calculated
- `min_buy_order_profit_in_percents`. The profit calculated by the algorithm
needs to exceed this minimum to place a buy order. The algorithm calculates
the difference between the current price and the last price (= candlestick) of
the most recent base.
- `previous_and_current_base_keep_coefficient` is used during the simulation
and helps to determine what is the most recent base. The most recent base is
held in variables, however when the current base is strong enough, the
current base becomes the most recent one.
- `profit_to_get_back_investment` represents the minimal profit in percents
to pay all fees and not to have losses.

In the `evaluate` function of the `CurrencyPairEvaluator` class:

- `max_old_price_bin_count` represents the number of price bins in which the
price interval (0, maximum of highs in the training data). Example: If
`max_old_price_bin_count == 200` and the highs of all candlesticks
in the training data are between 2000 USDT and 5000 USDT, then each price bin
is 25 USDT large (`5000 / 200 = 25`).
- `max_base_score` represents the upper bound of the base score. If the
calculation of the score results with the higher score, this value is used
instead. //TODO2 how is the score calculated
 
//TODO3
base_recognition_coefficient
 ... in np.linspace(1.7, 4, num=2)
 - median_volume = old_periods[PeriodColumns.VOLUME.column_name].median()
 - mean_volume = old_periods[PeriodColumns.VOLUME.column_name].mean()
 - multiplication_threshold = base_recognition_coefficient * mean_volume / median_volume
 
volume_unit_target_currency_to_buy_in_percents
 ... in np.linspace(0.75, 6, num=2)
 
//TODO parallel processing

//TODO drawing
