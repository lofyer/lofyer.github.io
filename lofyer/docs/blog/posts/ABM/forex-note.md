---
title: "外汇学习笔记"
date: 2017-10-22
categories: 
  - "abm"
  - "modeling"
tags:
  - "Agent-based Modeling"
  - "Forex Trading"
---

# 外汇学习笔记

早先把书的内容过了一遍，现在打算把书中的模型统统实现并做相应介绍。

## Agent based modeling Strategy in forex trading market

### PREFACE

Since I've tested S1, S2, and S3 from the [DATANOTE](https://datanote.readthedocs.io/zh/latest/). However, it was not so good or in other words, identical with expectation. It's time to make a change.

### BASIC MODEL AND ARCHITECTURE

As you can see from my last articles, [the architecture's infrastructure](https://blog.lofyer.org/the-forex-quantum-trade-system-and-infrastructure/) is about to go and the [agent's protocol](https://blog.lofyer.org/%E5%9F%BA%E4%BA%8E%E5%A4%96%E6%B1%87%E5%B8%82%E5%9C%BA%E6%8A%80%E6%9C%AF%E6%8C%87%E6%A0%87%E7%9A%84abm%E5%BB%BA%E6%A8%A1/) is well defined. Now what we should do is to combine them together.

In the first phase, the simulation will JUST be a Netlogo program. If the performance is just like S1-3, then it goes to phase 2 ---- treating the agents as the real from the data gained and strategy trained from the real world. And if NOT, less probable, the program must be corrected.

### PRACTICAL GUIDE

#### 1\. FAKE WORLD, FAKE AGENT

#### 2\. REAL WORLD, FAKE AGENT

#### 3\. REAL WORLD, REAL AGENT

World3 Model is a choice.(System Dynamics)

https://insightmaker.com/insight/1954/The-World3-Model-A-Detailed-World-Forecaster
---
title: "Forex data collection and backtesting with python/netlogo"
date: 2017-03-13
categories: 
  - "signal-system"
---

These two sites provides free forex tick/time data. So before getting into backtesting, we must get data.

I provide two scripts for you to download and update these data daily.

OK, it's boring...

[http://github.com/lofyer/forex-data](http://github.com/lofyer/forex-data)

However, there's still some work to update our data every day/week.

Like this:

TBD

For the next, we'll try to apply basic strategy to backtest our data.

Like this:

## Forex Data ML Relationship

Data Processing with ML

Data Overview: 1. Forex Index <-> Currency Rate Change 2. News -> Classification -> Weight <-> Currency Rate Change

Caution: Strict Time Based Data pipeline.

## 外汇常用技术指标算法（附Python量化分析教程）

公式不好写，写到DataNote里吧。

# Bill Williams Indicators

**Accelerator/Decelerator oscillator（AC）**

Acceleration/Deceleration (AC) technical indicator signals the acceleration or deceleration of the current market driving force.

_How to Use Acceleration/Deceleration_

The indicator is fluctuating around a median 0.00 (zero) level which corresponds to a relative balance of the market driving force with the acceleration. Positive values signal a growing bullish trend, while negative values may be qualified as a bearish trend development. The indicator changes its direction before any actual trend reversals take place in the market therefore it serves as an early warning sign of probable trend direction changes. The indicator color changes alone are very important as the current green line (irrespectively of the value) warns against long positions the same way as the red one would against selling. To enter the market along with its driving force one needs to watch for both value and color. Two consecutive green columns above the zero level would suggest you to enter the market with a long position while at least two red columns below the zero level might be taken for a command to go short. Fake signals prevail in timeframes smaller than 4 hours.

_Acceleration/Deceleration Oscillator Formula (Calculation)_

AC bar chart is the difference between the value of 5/34 of the driving force bar chart and 5-period simple moving average, taken from that bar chart.

AO = SMA(median price, 5)-SMA(median price, 34)
AC = AO-SMA(AO, 5)

\--------------------------------------------------------------------------- **Alligator**

Alligator is an indicator designed to signal a trend absence, formation and direction. Bill Williams saw the alligator's behavior as an allegory of the market's one: the resting phase is turning into the price-hunting as the alligator awakes so that to come back to sleep after the feeding is over. The longer the alligator is sleeping the hungrier it gets and the stronger the market move will be.

_How to Use Alligator Indicator_

The Alligator indicator consists of 13-, 8- and 5-period smoothed moving averages shifted into the future by 8, 5 and 3 bars respectively which are colored blue, red and green representing the alligator’s jaw, teeth and lips. The Alligator is resting when the three averages are twisted together progressing in a narrow range. The more distant the averages become, the sooner the price move will happen. The averages continuing in an upward direction (green followed by red and blue) suggest an emerging uptrend which we interpret as a signal to buy. The averages following each other in the reversed order down the slope are a strong signal of an unfolding downtrend so selling at this point would be more than appropriate.

Alligator Indicator Formula (Calculation)

MEDIAN PRICE = (HIGH + LOW) / 2
ALLIGATORS JAW = SMMA (MEDEAN PRICE, 13, 8)
ALLIGATORS TEETH = SMMA (MEDEAN PRICE, 8, 5)
ALLIGATORS LIPS = SMMA (MEDEAN PRICE, 5, 3)

\--------------------------------------------------------------------------- **Awesome oscillator（AO）**

Awesome Oscillator (AO) is a momentum indicator reflecting the precise changes in the market driving force which helps to identify the trend’s strength up to the points of formation and reversal.

_How to Use Awesome Oscillator_

There are three main signals which may be seen: Saucer - three consecutive columns above the nought line the first two of which must be colored red (the second one is lower than the first one) while the third one is colored green and higher than the previous (second) one. Such a formation would be a clear Buy signal whilst inverted and vertically flipped formation would serve as a Sell signal. Nought line crossing - the histogram crosses the naught line in an upward direction changing its values from negative to that of positive ones. In this situation we have a Buy signal. The Sell signal would be a reversed pattern. Two pikes - the indicator displays a Buy signal when the figure is formed by two consecutive pikes both of which are below the naught line and the later-formed pike is closer to the zero level than the earlier-formed one. The Sell signal would be given by the reverse formation.

_Awesome Oscillator Trading Strategy_

Awesome Oscillator Strategy includes 3 ways of trading. The first way is to open a sell position when the oscillator is below the zero line forming a peak, and open a buy position when the oscillator is above the zero line forming a gap. Another way is to open a sell position when the oscillator forms two peaks above the zero line, where the second high is lower than the previous one. And, conversely, traders watch to open a buy position when the oscillator forms to lows below the zero line with the last one not as low as the previous one. The third way is to account crossing the zero line. When the oscillator crosses it from up to down, it is time to open a sell position and when it crosses from down to up, it is time to open a buy position.

_Awesome Oscillator Formula (Calculation)_

Awesome Oscillator is a 34-period simple moving average, plotted through the central points of the bars (H+L)/2, and subtracted from the 5-period simple moving average, graphed across the central points of the bars (H+L)/2.
MEDIAN PRICE = (HIGH+LOW)/2
AO = SMA(MEDIAN PRICE, 5)-SMA(MEDIAN PRICE, 34)
where
SMA — Simple Moving Average.

\--------------------------------------------------------------------------- **Fractal Indicator Definition**

Fractals is an indicator highlighting the chart’s local heights and lows where the price movement had stopped and reversed. These reversal points are called respectively Highs and Lows. How to Use Fractal Indicator

Bill Williams' Fractals are formed around a group of five consecutive bars the first two of which are successively reaching higher (or diving deeper) and the last two descending lower (or growing higher) with the middle one being the highest (or the lowest) result in the group accordingly. Buy fractal is an arrow pointing to the top Sell fractal is an arrow pointing to the bottom -------------------------------------------------------------------------- **Gator Oscillator Definition**

The Gator Oscillator (GO) is a supplement to the Alligator indicator and is used alongside with it showing the absolute degree of convergence/divergence of the Alligator's three SMAs pointing at the Alligator's periods of slumber and awakeness (i.e. trending and non-trending market phases). How to Use Gator Oscillator

Being an oscillator in the form of two histograms built on either side of the naught line, the Gator Oscillator plots the absolute difference between the Alligator’s Jaw and Teeth (blue and red lines) in the positive area and the absolute difference between the Alligator’s Teeth and Lips (red and green lines) in the negative area. The histogram’s bars are colored green if exceeding the previous bar’s volume or red if falling short. The bars of the extreme values are in tune with the strong trend forces. The Alligator's activity periods are divided into the following four: Gator awakes - the bars on different sides of the naught line are colored differently. Gator eats - green bars on both sides of the naught line. Gator fills out - single red bar during the "eating" phase. Gator sleeps - the bars on both sides are red -------------------------------------------------------------------------- Market Facilitation Index Definition

The Market Facilitation Index is designed for evaluation the willingness of the market to move the price. The indicator's absolute values alone cannot provide any trading signals unlike it's dynamics in relation to the dynamics of the volume. How to Use Market Facilitation Index

The absolute values of the index are represented by the histogram's bars while the comparison of the index and volume dynamics are given in colors which are vital in terms of reading the indicator signs. Green bar - both MFI and volume are up. Increasing trading activity means market movement acceleration. We may join the trend. Blue bar - MFI indicator is up, volume is down. The movement is continuing although the volume has dropped. The trend will soon be reversing. Pink bar - MFI indicator is down, volume is up. The slowing down movement while volume is raising may indicate a possible break through, often a U-turn. Brown bar - both MFI and volume are down. The market is no longer interested in the current direction and is looking for signs of a future development. Market Facilitation Index Market Facilitation Index (BW MFI) Market Facilitation Index Formula (Calculation)

BW MFI = (HIGH-LOW)/VOLUME --------------------------------------------------------------------------

# Oscilators

# Trend Indicators

# Volume Indicators

**Accumulation/Distribution（AD）**

Accumulation/Distribution is a volume-based technical analysis indicator designed to reflect cumulative inflows and outflows of money for an asset by comparing close prices with highs and lows and weighting the relation by trading volumes.

_How to Use Accumulation/Distribution_

The Accumulation/Distribution line is used for trend confirmation or possible turning points identification purposes. Trend confirmation: An uptrend in prices is confirmed if A/D line is rising; A downtrend in prices is confirmed if A/D line is falling. Divergence pattern analysis: Rising A/D line along with decreasing prices indicates the downtrend may be weakening to a bullish reversal; Falling A/D along with rising prices indicates the uptrend may be weakening to a bearish reversal.

_Accumulation/Distribution Indicator Formula (Calculation)_

A/D(t) = \[((C – L) – (H – C)) / (H – L)\] x Vol + A/D(t-1),
where:
A/D(t) – current Accumulation/Distribution value;
A/D(t-1) – previous Accumulation/Distribution value;
H – current high;
L – current low;
C – close price;
Vol – volume.

\---------------------------------------------------------------------------

**Average Directional Movement Index（ADX）**

Average Directional Index (ADX) is a technical indicator developed by Welles Wilder to estimate trend strength and determine probable further price movements by comparing the difference between two consecutive lows with the difference between the highs.

_How to Use ADX Indicator_

ADX is a complex indicator, which results from calculation of the Plus Directional Indicator (+DI – green line) and the Minus Directional Indicator (-DI – red line), but all of them may be used for trend analysis.

In general the indicator (bold line) move is believed to reflect current trend strength: Rising ADX (usually climbing above 25) suggests strengthening market trend – trend following indicators are becoming more useful; Falling ADX suggests the trend development is doubtful. ADX values below 20 may indicate neutral trend is present – oscillators are becoming more useful. Use of complex ADX trading system may require additional confirmation signals: Normally if +DI (green line) climbs above -DI (red line), a buy signal is generated; Normally if -DI climbs above +DI, a sell signal is generated. Average Directional Index Indicator - ADX Indicator Average Directional Index (ADX) Indicator

_ADX Trading Strategy_

ADX trading strategy aims to identify the strongest trends and distinguish between trending and non-trending conditions. ADX reading above 25 indicates trend strength, while when ADX is below 25, this shows trend weakness. Breakouts, which are not difficult to spot, also help to identify whether ADX is strong enough for the price to trend or not. Thus, when ADX rises from below 25 to above 25, trend is considered strong enough to continue in the direction of the breakout. It’s a common misperception that when ADX line starts falling this is a sign of trend reversal. Whereas, it only means that the trend strength is weakening. As long as ADX is above 25, it should be considered that a falling ADX line is simply less strong.

ADX Formula (Calculation)

ADX = MA \[((+DI) – (-DI)) / ((+DI) + (-DI))\] x 100;
where:
+DI – Plus Directional Indicator;
-DI – Minus Directional Indicator.

\---------------------------------------------------------------------------

**Average True Range（ATR）** --------------------------------------------------------------------------- **Bears Power** --------------------------------------------------------------------------- **Bollinger Bands** --------------------------------------------------------------------------- **Bulls Power** --------------------------------------------------------------------------- **Commodity Channel Index（CCI）** --------------------------------------------------------------------------- **DeMarker** --------------------------------------------------------------------------- **Envelopes** --------------------------------------------------------------------------- **Force** --------------------------------------------------------------------------- **Fractals** --------------------------------------------------------------------------- **Gator oscillator** --------------------------------------------------------------------------- **Ichimoku Kinko Hyo** --------------------------------------------------------------------------- **Market Facilitation Index（BWMFI）** --------------------------------------------------------------------------- **Momentum** --------------------------------------------------------------------------- **Money Flow Index** --------------------------------------------------------------------------- **移动平均线（Moving Average，MA）**

MA=N日收市价之和／N
一般N可取短期5或10天，也可是30、65、200、280等中长期。

Moving Average is a technical analysis tool that shows average price over a given period of time, which is used to smoothen price fluctuations and therefore to determine trend direction and strength. Depending of the method of averaging, distinguish between simple moving average (SMA), smoothed moving average (SMMA) and exponential moving average (EMA).

_Moving Average Trading Strategy_

Moving average strategy is essentially a trend following means. Its objective is to signal the beginning of a new trend or a trend reversal. Herein, its main purpose is to track the progress of the trend and not to predict market action in the same sense that technical analysis attempts to do. By its nature, Moving Average is a follower; it follows the market telling that a new trend has begun or reversed only after the fact.

_Moving Average Formula (Calculation)_

SMA = Sum (Close (i), N) / N,
where:
Close (i) – current close price;
N – period of averaging.
EMA(t) = EMA(t-1) + (K x \[Close(t) – EMA(t-1)\]), 
where:
t – current period;
K = 2 / (N + 1), N – period of averaging.

_How to Use Moving Average_

Generally moving average curves analysis includes the following principles: Direction of moving average curve reflects prevailing trend over a period; Low-period averaging may give more false signals, while large-period averaging tend to be lagging; To increase (decrease) sensitivity of the curve one should decrease (increase) the period of averaging; Average curves are more useful in trending environment.

Comparing moving average with price movements: A strong buy (sell) signal arise if price crosses from below (from above) its rising (falling) moving average curve; A weak buy (sell) signal arise if price crosses from below (from above) its falling (rising) moving average curve. Comparing moving average curves of different periods: A rising (falling) lower-period curve crossing from below (above) another rising (falling) longer-period curve gives a strong buy (sell) signal; A rising (falling) lower-period curve crossing from below (above) another falling (rising) longer-period curve gives a weak buy (sell) signal. --------------------------------------------------------------------------- **Moving Average of Oscillator** --------------------------------------------------------------------------- **抛物线转向（Stop And Reverse）**

SAR（n）=SAR（n－1）+AF\*\[EP(n-1)-SAR(n-1)\]

其中：SAR（n）为第n日的SAR值，SAR（n－1）为第（n－1）日的值；AF为加速因子（或叫加速系数），EP为极点价（最高价或最低价）。

# -\*- coding: utf-8 -\*-

"""
http://virtualizedfrog.wordpress.com/   2014

Translated from http://www.amibroker.com/library/detail.php?id=268
Requires pandas to load csv files, and matplotlib to chart the data

The main expects table.csv file. Valid files can be downloaded on Yahoo Finance
eg: http://real-chart.finance.yahoo.com/table.csv?s=%5EGSPC&ignore=.csv
"""

import pandas as pd
from datetime import datetime
import matplotlib.pyplot as plt

def psar(barsdata, iaf = 0.02, maxaf = 0.2):
    length = len(barsdata)
    dates = list(barsdata\['Date'\])
    high = list(barsdata\['High'\])
    low = list(barsdata\['Low'\])
    close = list(barsdata\['Close'\])
    psar = close\[0:len(close)\]
    psarbull = \[None\] \* length
    psarbear = \[None\] \* length
    bull = True
    af = iaf
    ep = low\[0\]
    hp = high\[0\]
    lp = low\[0\]
    
    for i in range(2,length):
        if bull:
            psar\[i\] = psar\[i - 1\] + af \* (hp - psar\[i - 1\])
        else:
            psar\[i\] = psar\[i - 1\] + af \* (lp - psar\[i - 1\])
        
        reverse = False
        
        if bull:
            if low\[i\] < psar\[i\]:
                bull = False
                reverse = True
                psar\[i\] = hp
                lp = low\[i\]
                af = iaf
        else:
            if high\[i\] > psar\[i\]:
                bull = True
                reverse = True
                psar\[i\] = lp
                hp = high\[i\]
                af = iaf
    
        if not reverse:
            if bull:
                if high\[i\] > hp:
                    hp = high\[i\]
                    af = min(af + iaf, maxaf)
                if low\[i - 1\] < psar\[i\]:
                    psar\[i\] = low\[i - 1\]
                if low\[i - 2\] < psar\[i\]:
                    psar\[i\] = low\[i - 2\]
            else:
                if low\[i\] < lp:
                    lp = low\[i\]
                    af = min(af + iaf, maxaf)
                if high\[i - 1\] > psar\[i\]:
                    psar\[i\] = high\[i - 1\]
                if high\[i - 2\] > psar\[i\]:
                    psar\[i\] = high\[i - 2\]
                    
        if bull:
            psarbull\[i\] = psar\[i\]
        else:
            psarbear\[i\] = psar\[i\]

    return {dates:dates, high:high, low:low, close:close, psar:psar, psarbear:psarbear, psarbull:psarbull}

if \_\_name\_\_ == "\_\_main\_\_":
    import sys
    import os
    
    if len(sys.argv) < 2:
        sys.exit("Usage: %s datafile.csv" % sys.argv\[0\])
    if not os.path.exists(sys.argv\[1\]):
        sys.exit("Error: can't open file '%s': No such file" % sys.argv\[1\])

    barsdata = pd.read\_csv(sys.argv\[1\])
    #Reindex the data: ascending dates are expected in the function
    barsdata = barsdata.reindex(index=barsdata.index\[::-1\])
    #Convert strings to actual timestamps
    barsdata\['Date'\] = \[datetime.strptime(x, '%Y-%m-%d') for x in barsdata\['Date'\]\]

    startidx = 0
    endidx = len(barsdata)
    
    result = psar(barsdata)
    dates = result\['dates'\]\[startidx:endidx\]
    close = result\['close'\]\[startidx:endidx\]
    psarbear = result\['psarbear'\]\[startidx:endidx\]
    psarbull = result\['psarbull'\]\[startidx:endidx\]
    
    plt.plot(dates, close)
    plt.plot(dates, psarbull)
    plt.plot(dates, psarbear)
    plt.grid()
    plt.show()

动向指标（DMI） --------------------------------------------------------------------------- **Moving Averages Convergence/Divergence（MACD）** --------------------------------------------------------------------------- On Balance Volume（OBV） --------------------------------------------------------------------------- 相对强弱指标（Relative Strength Index，RSI） --------------------------------------------------------------------------- Relative Vigor Index（RVI） --------------------------------------------------------------------------- 漫步随机指标（Random Walk Index,RWI） --------------------------------------------------------------------------- Standard Deviation（StdDev） --------------------------------------------------------------------------- Stochastic Oscillator --------------------------------------------------------------------------- Larry Williams' Percent Range（WPR） --------------------------------------------------------------------------- [Python量化分析中文](http://blog.lofyer.org/wp-content/uploads/python-quant-uqer.pdf)

## Get ready for forex

这个月书的样章要完结，还有一篇稿要写。

偶然看到高频交易，加上之前对股市的自动交易感兴趣，然后偶然看到外汇交易，metatrader，看到他们的交易策略，我就燃了。（亏）新手轻虐。

Scott E.Page是个好老师。

节奏要加快，再快，再快点。

# 基于外汇市场技术指标的ABM建模

本模型的最终模拟期望是随机，我想试试。

货币对作为agent，这样仅需要个位数以内；

货币对本身不拥有市场价值，初始值为此货币对相对于eurusd的比值；

agent拥有自己的技术指标，这个指标尽量在一定范围内震荡；

agent行动时，随之变化的指标会影响其他agent的行动；

patch横向分层，高中低区，agent的行动只在patch间行动；
