---
layout: post
title: "Hurst exponents of cryptocurrencies"
date: 2018-04-29 12:00:00 -0700
category: time-series
---

Cryptocurrency has been getting a lot of attention. There have been several resources on prediction of cryptocurrencies, but this post simply look at some characteristics of the closing price of some of the cryptocurrencies. Primarily, the the self-similar exponent or the Hurst exponent will be extracted using the [rescaled-range analysis](https://en.wikipedia.org/wiki/Rescaled_range) or R/S analysis, which has been used to investigate correlations in time-series data. The distribution of the data may also serve as a distinguishing characteristic.

The data used is a [sample database](https://blog.timescale.com/analyzing-ethereum-bitcoin-and-1200-cryptocurrencies-using-postgresql-downloading-the-dataset-a1bbc2d4d992) provided from [TimescaleDB](https://www.timescale.com/). The data ranges from `2010-07-16` to `2017-06-26` for over 1200 cryptocurrencies. For this post, the database is stored as PostgreSQL and processed in Python via [SQLAlchemy](https://www.sqlalchemy.org/). The closing price is measured at the end of the day, which then determines the minimum frequency of this time-series ($$\tau_{min} = 1$$ day).

---
## Bitcoin

The most prominent cryptocurrency is Bitcoin (BTC). The historical trend of the closing price (`closing_price`) is shown below with its difference. This difference in time is then used to calculate the R/S, and the Hurst exponent is estimated.
![BTC closing price](/assets/btc_closing_price_plt.png)

The R/S and auto-correlation (ACF) plots are shown below. The Hurst exponent in the range $$t \in [100, 1000]$$ days is close to random. Here, the R/S is calculated by taking a time step of $$\tau^{1.2}$$, which generates evenly spaced time lags for R/S calculation. The R/S analysis overestimates the Hurst exponent of a normally distributed signal ($$H \sim 0.55$$). The ACF plot confirms this by showing a rapid decrease and a decorrelation time of about $$\tau_D \sim 7$$ days. This suggests that the signal has a short memory. Both of these plots suggest that the time sequence resembles a normally distributed statistics.
![BTC R/S and ACF](/assets/btc_rsplt.png | width=200)

Although both the R/S and ACF plots suggest a normally or Gaussian distributed statistics in the time sequence, the probability density function (PDF) has a power-law character despite $$H \sim 0.55$$. Hence, the values are sampled with a distribution that has a heavier tail than a normal or Gaussian distribution.
![BTC difference of closing price PDF](/assets/btc_pdfplt.png)

The distinction between a distribution with a power-law tail is shown below. The Gaussian distribution has exponential tails whereas power-law distributions have heavier tails.
![PDF sample](/assets/pdf-sample.png | width=200)

---
## Other cryptocurrencies

Now, after looking at a specific example of a successful cryptocurrency, we can treat other cryptocurrencies in a similar manner. We select a few of the "successful" cryptocurrencies by looking at the total traded volume. According to this dataset as of mid-2017, apart from Bitcoin (BTC), there are Ethereum (ETH), Litecoin (LTC), Ethereum Classic (ETC), Ripple (XRP), Monero (XMR), and Dash (DASH).
![Cryptocurrency closing price](/assets/crypto_closing_price_plt.png)

| Cryptocurrency   | Currency code | H           | $$\tau_D$$ (days) |
|:-----------------|:-------------:|:------------|:-----------------:|
| Bitcoin          | BTC           | 0.55        | 7.                |
| Ethereum         | ETH           | 0.54        | 2.0               |
| Litecoin         | LTC           | 0.46        | 1.7               |
| Ethereum Classic | ETC           | 0.68        | 1.8               |
| Ripple           | XRP           | 0.54        | 2.5               |
| Monero           | XMR           | 0.48        | 1.9               |
| Dash             | DASH          | 0.34        | 1.7               |

As an example of the distribution of closing prices, the distribution below shows the distribution of the difference in the closing price for ETH. There are some differences from BTC.
![ETH difference of closing price PDF](/assets/ETH_pdfplt.png)

The distribution of LTC is shown below for comparison. There are notable differences between BTC and ETH.
![LTC difference of closing price PDF](/assets/LTC_pdfplt.png)

---
## Remarks

There are more notable differences in the PDFs than the estimation of the Hurst exponent. This may be due to the lack of stationarity of the price signals. There are more metrics to describe the health of a cryptocurrency. Apart from the trading data, both the community support and development activity may need to be considered.

---
## References
- Sarah Pan, [Analyzing Ethereum, Bitcoin, and 1200+ other Cryptocurrencies using PostgreSQL: Downloading the Dataset](https://blog.timescale.com/analyzing-ethereum-bitcoin-and-1200-cryptocurrencies-using-postgresql-downloading-the-dataset-a1bbc2d4d992), Medium, 2017-06-27.
- Sarah Pan, [Analyzing Ethereum, Bitcoin, and 1200+ other Cryptocurrencies using PostgreSQL](https://blog.timescale.com/analyzing-ethereum-bitcoin-and-1200-cryptocurrencies-using-postgresql-3958b3662e51), Medium, 2017-06-28.
- B. B. Mandelbrot and J. R. Wallis, [Noah, Joseph, and Operational Hydrology](http://dx.doi.org/10.1029/WR004i005p00909), Water Resources Res., 4 (5), 1968, 909 - 918.
- H. E. Hurst, Long term storage capacity of reservoirs, Trans. Amer. Soc. Civil Eng., 116 (1), 1951, 770 - 799.
