In preparation for integration with Morpho, I'm doing a read through of their [whitepaper](https://whitepaper.morpho.xyz) and docs. It's very accessible and anyone reading this to read it yourself in full. This document contains my notes and analysis.

![](abstract.png)

One thing that's made clear from is that the Morpho of today is just the beginning! I believe that short duration secured lending as in the repo markets is one of the most natural parts of the financial system to move on chain in the near future and take advantage of greatly reduced settlement costs. The endgame here is not an arbitrary rate or interest curve, but one that emerges from market supply and demand. This is what we're working on with the new VOLT rate as part of market governance.

![](compete.png)

The main advantage of a pool model is the simple and reliable user experience around liquidity. Instead of lenders needing to actively manage their own liquidity, the pool's interest rate model ensures that there are(almost) always sufficient funds liquid for them to withdraw.

The Morpho paper does a better job than I will explaining the losses this enforces in the spread. What I would highlight here is that while Compound suppliers are described as "not competing with each other" (which is true in regards to rates), this is one important dimension in which pool lending is highly competitive.

In the event of a liquidation failure that exceeded the reserves' capacity to absorb, or any kind of successful attack that resulted in loss of funds, a race condition is created between Compound suppliers. Those who are first to withdraw take no loss, those who are too late cannot withdraw at all.

Volt Protocol already has features intended to allow emergency removal of funds in a venue in the event of losses or heightened risk conditions, and we are working on an expanded PCV Sentinel System.

![](parameter.png)

Those who have been following us for a while know that VOLT used to offer a yield rate pegged to the consumer price index. Arbitrary targets mean inefficient markets. One can envision a Compound variant whose A and B parameters are adjusted by a controller that takes time-weighted average utilization as its input.

