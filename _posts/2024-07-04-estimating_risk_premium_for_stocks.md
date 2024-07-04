---
layout: post
title: Estimating Risk Premium for Stocks/Equity
categories: [Fin]
---

There are 3 ways to estimate risk premium that you can consider:
1. Survey Premiums (survey investors)
2. Historical Risk Premiums (historical data)
3. Implied Risk Premiums (estimating from today's asset price) *(Best way)*

**Survey Risk Premiums**
From Damodaran's slide this translate to the following table:

| Group Surveyed          | Survey Done by                    | Estimated ERP | Notes                       |
|-------------------------|-----------------------------------|---------------|-----------------------------|
| Individual Investors    | Securities Industries Association | 8.3 % (2004)  | One year premium            |
| Institutional Investors | Merrill Lynch                     | 4.8 % (2013)  | Monthly updates             |
| CFOs                    | Campbell Harvey & Graham          | 4.48 (2012)   | 5-8% response rate          |
| Analysts                | Pablo Fernandez                   | 5.0% (2011)   | Lowest Standard deviation   |
| Academics               | Pable Fernandez                   | 5.7% (2011)   | Higher for emerging markets |


*Problems: These values depend on people's choice and carries no rationality. Also, it depends on people's who turn up to response and their personal bias from their previous experience.*

**Historical Risk Premiums**
The naivest way to calculate this from earlier investments is by subtracting the returns earned from a stock from the return earned from a risk-free asset.
He also provides a snapshot of historical average rate of return based on T-bills (short term: less than a year) and T-bonds (long term: 10-30 years). 

*Problems: The variation of this value depends heavily on the timeframe of observation. Also, the long-term rate of rate is close to its standard deviation, implying basically a noise.*

**Implied Risk Premium**
This is done with minimum assumptions or guesswork on long term rate of return.
The key ingredients are:
1. A solid and popular index. Example:  NIFTY50, BANKNIFTY
2. Value of Dividends and Buybacks for the last 12 months (TTM)
3. Analyst's estimate on the growth of the index in the next 5 years
4. Current risk-free rate of return (long term: T-bond rate)

In this approach of Risk premium analysis, we convert the payment of a stock or an index to an equivalent coupon bond, where payments are stable.

I am using the same example as Mr. Damodaran used.
Let the current price of the index = 1756.54
This is the cash to investor (E).

Let the stocks under the index paid a total dividend of 33.33 in the trailing twelve months (TTM).
And the total return to the investor from share buybacks (TTM) were 49.02 
Therefore, the total earning to the investor in TTM was $33.33+49.02=82.35$

Now we will use the analyst's expected rate of return. Let that be $5.59\%$

Then, 1st year from now, we can expect to receive a payment of $82.35\*1.0559=86.96$. 
In 2nd year this will be $86.96*1.0559=91.82$ and so on.
We will estimate till 5 years, and then we get as per the risk-free rate of return. 
Let that risk free rate of return be $2.55\%$. We use T-bonds because it can span for our long-term projection of 5 years.

$$
1756.54 = \frac{86.96}{(1+r)} + \frac{91.82}{(1+r)^2} +  \frac{96.95}{(1+r)^3} +  \frac{102.38}{(1+r)^4} \\+  \frac{108.10}{(1+r)^5} +    \frac{110.86}{(r - 0.0255)(1+r)^5}
$$

Solving this manually is mathematically challenging, so we can use some tool like SOLVER in MS-EXCEL, or python.
Upon solving the above equation, we will get 
$r = 8.04\%$  = Implied rate of return on Stocks 

But we already get  $2.55\%$ from risk-free rate of return.
Therefore, the actual return that we get for taking risks by investing in Stocks = $r - 2.55 = 5.49$

Implied equity risk premium = $5.49\%$

This is the value we can use in CAPM model, as well as in the calculation of Weighted Average Cost of Capital; when a corporation raises money in the form of Equity.

** Expected Return Calculation for NIFTY 50**
I found an excellent example of calculation for the expected return calculation for NIFTY 50 index, where they used the exact same blueprint described in the slides of Mr. Damodaran and also discussed here.
Here is the [link](https://www.rvoicmai.in/e-book/Nifty-50-Valuation-Report-myakjj0lEsfIossk).


**References**
1. [Damodaran Slide](https://pages.stern.nyu.edu/~adamodar/podcasts/cfspr23/session7slides.pdf)
2. [Damodaran Video](https://www.youtube.com/watch?v=J1m0PKrQrH4)
3. [E-library: Valuation books for India](https://www.rvoicmai.in/e-library) 
4. [Historical Valuation of Indian Equity Market: An Excellent Site](http://www.market-risk-premia.com/in.html)
