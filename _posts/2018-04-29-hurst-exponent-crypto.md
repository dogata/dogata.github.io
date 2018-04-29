---
layout: post
title: "Hurst exponents of cryptocurrency dataset"
date: 2018-04-29 12:00:00 -0700
category: time-series
---

Cryptocurrency has been getting a lot of attention. There have been several resources on prediction of cryptocurrencies, but this post simply look at some characteristics of the closing price of some of the cryptocurrencies. Primarily, the the self-similar exponent or the Hurst exponent will be extracted using the [rescaled-range analysis](https://en.wikipedia.org/wiki/Rescaled_range) or R/S analysis, which has been used to investigate correlations in time-series data. The distribution of the data may also serve as a distinguishing characteristic.

The data used is a [sample database](https://blog.timescale.com/analyzing-ethereum-bitcoin-and-1200-cryptocurrencies-using-postgresql-downloading-the-dataset-a1bbc2d4d992) provided from [TimescaleDB](https://www.timescale.com/). The data ranges from `2010-07-16` to `2017-06-26` for over 1200 cryptocurrencies. For this post, the database is stored as PostgreSQL and processed in Python via [SQLAlchemy](https://www.sqlalchemy.org/). The closing price is measured at the end of the day, which then determines the minimum frequency of this time-series ($\tau_{min} = 1$ day).

The most prominent cryptocurrency is Bitcoin (BTC). The historical trend of the closing price (`closing_price`) is shown below with its difference. This difference in time is then used to calculate the R/S, and the Hurst exponent is estimated.
![BTC closing price](/assets/btc_closing_price_plt.png){ width=50% }

The R/S and auto-correlation (ACF) plots are shown below. The Hurst exponent in the range $t \in [100, 1000]$ days is close to random. Here, the R/S is calculated by taking a time step of $\tau^{1.2}$, which generates evenly spaced time lags for R/S calculation. The R/S analysis overestimates the Hurst exponent of a normally distributed signal ($H \sim 0.55$). The ACF plot confirms this by showing a rapid decrease and a decorrelation time of about $\tau_D \sim 7$ days. This suggests that the signal has a short memory. Both of these plots suggest that the time sequence resembles a normally distributed statistics.
![BTC R/S and ACF](/assets/btc_rsplt.png){ width=50% }

Although both the R/S and ACF plots suggest a normally or Gaussian distributed statistics in the time sequence, the probability density function (PDF) has a power-law character despite $H \sim 0.55$. Hence, the values are sampled with a distribution that has a heavier tail than a normal or Gaussian distribution.
![BTC PDF](/assets/btc_pdfplt.png){ width=50% }

The distinction between a distribution with a power-law tail is shown below.
![PDF sample](/assets/pdf-sample.png){ width=50% }
