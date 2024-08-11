---
layout: post
title: Put-Call Parity and Finding Arbitrage Oppurtinity
categories: [Fin]
---

Put-call parity of an option is the relationship between the price of a call option and the price of a put option when four conditions are met:
1. Both the call and put should be of European type.
2. Both the call and put should be of the same underlying asset
3. Both the call and put should be having the same Strike price
4. Both the call and put should be having the same expiry date.
5. The stock do not pay any dividend.

Pricing of options maintaining the put-call parity relationship will give a fair pricing model which do not provide any opportunity of market arbitrage.
However, whenever this relation do not hold true, arbitrage is possible which gives risk free return on investment in the option.

Let me first show the use of put-call parity into action without going into the technicalities. Concretely, I will try to show how to identify arbitrage opportunity.

The equation we will use is:
Call (c) + Present Value of Strike (Ke$^{-rT}$) = Put (p) + Spot (S)

Now lets say, an underlying has a spot price of Rs 31, and for a stike of Rs. 30, the call option is trading at Rs. 3, the put option is trading at Rs. Rs. 2.25. The option has 3 months to expiry, and the market risk free rate is 10%
Therefore, left hand side of put-call parity will be $3 + 30e^{-0.1 \times \frac{3}{12}} = 32.26$. And the right hand side will be $2.25 + 31 = 33.25$.
Clearly, the two sides do not match. Thus if we sell the asset on the right hand side and buy the asset on the left hand side, we will make an arbitrage profit of Rs. 0.99.

## History and the meaning of Put-call parity relation

**History**
In 1973, when Chicago Board of Options Exchange (CBOE) began trading, there were only call options. Although the concept of call and put options were known to mankind since 17<sup>th</sup> century, put option hadn't been standardized from the perspective of pricing model.
In 1977, put option had been introduced to the exchanges after the famous Black Scholes Pricing Model was developed by two professors, Fisher Black and Myron Scholes; in the year 1973.

Although adopted very late, the idea of put-call parity is believed to have originated from Russell Sage (1816-1906 *i.e late 19th century*). He used the principle of put-call parity to devise synthetic loans that were created by him buying stock and a related put from a customer. Sage eventually stopped trading in his way because of significant losses.

**Theory of Put-call parity**
The profit diagram of a call and put option is given as follows:
![Synthetic forward contract]({{site.baseurl}}/assets/img/2024-06-30-put_call_parity_fig1.png)

The diagram shows that the net profit/loss line upon buying a call option and selling a put option of the same asset, same expiry, and same strike price. We see that the combined effect of call and put gives rise to a similar situation as in the case of a forward contract. We call this synthetic forward. The strike price of the forward is the same as that of the options. Now, what is this synthetic long forward at any given point in time in future? 
To answer this through the lens of options, we will first understand the situation individually in the two possible cases: when the underlying asset is trading above the strike price, and when it is trading below the strike price.

*First case:* Lets say at time T in future, the price of the underlying is trading *above* the current strike price. 
In this case (orange line), the payoff will be, $max(S(T) - K, 0) - c$. Why? Because we will only gain as much as the stock price moved above the stike price, else we wont exercise the call option. But we will also have to deduct the primium paid in purchasing the call option. Hence the deduction.

*Second case:* Lets say at time T in future, the price of the underlying is trading *below* the current strike price. 
In this case (blue line), the payoff will be, $p - max(K - S(T), 0)$. Why? Because we collect the premium that adds to our gain, but we will also have to accept the loss as much as the stock moved below the strike price.

Now lets revisit the situation in the *First case* when both call and put options were bought and sold together (green line). Since we have taken call and put position at the same time, same strike price, and on the same expiry date, the net price of the stock for us on expiry will be:
$(c - p) + K$. 
The is because we paid money to buy the call option, pocketed money of put, and we also then have to buy the shares at $K$ per unit at expiry (although the shares actually cost more than $K$ i.e. profit). This is right now the net price for us of the synthetic forward ($S_0$).

In the *Second case*, this will be $(c - p) + K$. Yes, the same. Because here, the exercise price `(c - p)` will also be there, and we will be obligated to buy the shares at $K$ unit each (although the shares actually cost less than $K$ i.e. loss)

Therefore, the price of the synthetic forward effectively becomes $(c - p) + K$. We created this systhetic forward by buying call and selling put together.

Now, there is only one problem over here. The strike price ($K$) we used, is what we got from option's strike price; which is a price T time ahead from present. 
And in option trading, eveything is settled in cash on a M2M basis, unless we take the delivery.
Hence, to calculate the effective strike price for us of the synthetic forward contract on the present date, we need to discount $K$ with any available risk-free rate of interest (r).
This gives us the strike price ($S_0$) of the synthetic forward contract as $(c - p) + Ke^{-rT}$. If this matches the currently traded value of the underlying (i.e the spot price), there is no scope of arbitrage by hedging on the risk-free rate of interest. It gives us the put-call parity relation.
$S_0 = c - p + Ke^{-rT} = S$

If $S_0$ \> current spot price ($S$) , it means the stock is undervalued, and if $S_0$ \< current spot price ($S$), it means the stock is overvalued.

How to use the arbitrage opportunity to hedge:
1. Rewriting the derived put-call parity equation as stated at the very beginning: $c + Ke^{-rT} = p + S$.
    >For a dividend paying stock, the put-call parity equation becomes:
    >$c + Ke^{-rT} = p + Se^{-r_{d}T}$
2. If the right hand side is more than the left hand side, we will sell the more and buy the less one immediately.
3. At this point, I stumbled at first on the way to buy the exponential part on the left side. The short answer is, buy a bond or FD of the amount $Ke^{-rT}$ where the rate of risk-free interest is $r$. 

