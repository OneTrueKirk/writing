# Morpho Whitepaper Notes <!-- omit in toc -->

## Contents <!-- omit in toc -->
<details>

<summary></summary>

- [The Morpho Opportunity](#the-morpho-opportunity)
- [Advantages of Aggregated Lending](#advantages-of-aggregated-lending)
- [Underlying Venue Liquidity Risk](#underlying-venue-liquidity-risk)
  - [Volt Protocol Migitations](#volt-protocol-migitations)
  - [The Future of Morpho; Beyond Lending Pools](#the-future-of-morpho-beyond-lending-pools)

</details>

# The Morpho Opportunity

In preparation for integration with Morpho, I'm doing a read through of their [whitepaper](https://whitepaper.morpho.xyz) and docs. It's very accessible and anyone reading this to read it yourself in full. This document contains my notes and analysis. For this article, quotes will be as follows:

```
Morpho enhances current DeFi liquidity protocols. The aim is to offer a suite of products to make supplying and borrowing operations in DeFi more efficient and seamless. The first building block proposed is a novel, Pareto-improving, interest rate mechanism built on top of existing protocols. The Morpho Protocol allows for better rates on both sides of the market whilst preserving the same liquidity and liquidation guarantees for everyone.
```

One thing that's made clear from is that the Morpho of today is just the beginning! Short duration secured lending, such as in the 3 trillion dollar US repo market, is one of the most natural parts of the financial system to move on chain in the near future and take advantage of greatly reduced settlement costs. Traditional systems with limited operating hours (overnight risk) and higher switching costs should give way to always online, minimal friction public alternatives.

The endgame here is not an arbitrary rate or interest curve, but one that emerges from market supply and demand. This is what we're working on with the new VOLT rate as part of market governance, as well as around VCON holders being able to price assets and market make with orders.

```
Compound is relying on a Peer-to-Pool model where suppliers deposit their liquidity in a common pool and get tokenized vouchers in return, known as cTokens3. Any borrower can supply collateral to access liquidity from this pool. When repaying their position, borrowers pay some interest that also goes into the pool. Notice that under this model, positions do not have a fixed term and that suppliers are not competing with each other: the interest paid is shared amongst all suppliers, proportionally to the amount supplied. In practice, suppliers are committing much more capital to the pool than is ultimately utilized. Large amounts of capital thus remain unused in the pool and mechanically, the experienced supply APY is lower than the borrow APY.
```

The main advantage of a pool model is the simple and reliable user experience around liquidity. Instead of lenders needing to actively manage their own liquidity, the pool's interest rate model ensures that there are(almost) always sufficient funds liquid for them to withdraw. The downside is that maintaining a target amount of idle liquidity means less profits to lenders and higher rates for borrowers, an overall less than efficient market.

Morpho's peer to peer engine is at least as liquid as the underlying market, so long as its size does not exceed that market. However, peer to peer matching at large scale can indeed break atomic liquidity. Morover, as described below, a system where lenders can compete on rates will improve market outcomes.

```
However, this model has proven very inefficient as suppliers are not competing with each other. Moreover, one may also remark that rates are not decided by the offer and demand of the market but are biased by A and B parameters, chosen by the platform. A natural idea would be to build some sort of order book to register every position in a P2P fashion.
```

# Advantages of Aggregated Lending

Much like the transition from Uniswap v2 to Uniswap v3, this is a major step forward but also signficant complexity for many users to manage actively, and is only gas efficient for positions above a certain size.

Lenders can benefit by socializing as in the case of Volt Protocol. The many small and large VOLT holders mint VOLT in exchange for capital that the system will allocates through VCON market governance, with the goal of obtaining optimized yield. VOLT has liquidity management built in with a VOLT rate that adjusts based on the size of the surplus buffer such that excess redemption demand results in rate increases and vice versa.

By holding a mix of liquidity profiles including portions of fully liquid reserves, VOLT protocol can offer a better experience to yield-seekers than lending directly.

```
• Economic Efficiency: Maximize matched volumes.
• Gas Efficiency: Minimize gas, avoid dust problems.
• Simplicity: A passive user or contract should be able to benefit from Morpho simply supplying/borrowing.
• Fairness: The use of Morpho should benefit as many users as possible.
```

The design considerations around P2P matching are interesting, and I'm curious whether the Morpho team will explore alternative models in future iterations. The whitepaper doesn't quite maker clear how the matching engine works, but the docs are more informative.

```
How matching works

To match users, the Morpho protocol holds an on-chain priority queue, sorting users according to the amount they want to lend or borrow. When a new lender supplies liquidity to the protocol, their liquidity is matched with the largest borrower first. The second is down until the liquidity supplied is fully matched or there are no more borrowers to match.

Symmetrically, suppose a new borrower asks for liquidity to the protocol. In that case, their demand is matched with the most significant lender first, then the second, down until the borrowed liquidity is fully matched or there are no more lenders to match.

The data structure holding the priority queue on-chain is chosen based on research led by Morpho Labs on the most gas-efficient data structure for this task.

To be fully scalable, the Morpho DAO sets a maximum gas consumption for the matching process. This parameter is known as maxGasForMatching.

Advanced users can customize this parameter when supplying or borrowing liquidity. If the matching gas consumption exceeds maxGasForMatching, the remaining unmatched liquidity is deposited on the underlying protocol's pool.
```

As you can see, this means that larger lenders are likely to enjoy higher yield on Morpho as a result of taking precedence in the priority queue and spending more time fully peer to peer matched. This can be considered fair because it is also more costly for borrowers to have to iterate over a larger number of suppliers. The fewer they have to look over to make a successful peer to peer match, the lower the costs of the system and the higher the peer to peer rate. This naturally leads to a process where matches occur until it reaches the marginal user size where it is no longer profitable to do so, or more likely all the demand on one side is satisfied.

VOLT holders would benefit from higher rates on Morpho than they would depositing into the platform individually.

# Underlying Venue Liquidity Risk

Morpho-Compound v2 does not add any market risk to the underlying venue, unless as mentioned above it is larger than the underlying venue and the p2p matching instills liquidity risk.

Liquidity risk can however arise from the underlying venue itself, and is worth thinking about for Morpho lenders. In the event of a liquidation failure that exceeded the reserves' capacity to absorb, or any kind of successful attack that resulted in loss of funds, a race condition is created between Compound suppliers. Those who are first to withdraw take no loss, those who are too late cannot withdraw at all.

## Volt Protocol Migitations

Volt Protocol has features intended to allow emergency removal of funds in a venue in the event of losses or heightened risk conditions, and we are working on an expanded PCV Sentinel System by which any MEV searcher can be rewarded for providing evidence of bad debt positions and triggering the removal of PCV from venues.

The objective of these features is to prevent loss to VOLT holders if possible, and if not ensure that losses are fairly socialized among VOLT holders. The system will seek to prevent sophisticated actors, or those who are simply fortunate to be awake at the time of market volatility or exploit, from avoiding losses while holders who are slower to respond bear the brunt.

## The Future of Morpho; Beyond Lending Pools

```
3.2 Chrysalis
Caterpillars are great but they have one weakness, the pool. If the pool is not liquid anymore, the Caterpillar is not liquid as well. Taking a step back, we realize that in traditional and decentralized finance, liquidity can either come from idle pools or from activity, where market makers constantly put the liquidity where it is the most needed.
Similarly to Uniswap V3 [AZS+21], one solution would be to give the user the flexibility to choose between different P2P APYs (eg. the mid-rate, a rate a bit lower but still inside the spread, a rate a bit higher but still inside the spread)
On this observation, it appears that Caterpillar could become a Chrysalis, by ac- cepting active users and posting liquidity promises at certain rates, within the spread of the pool. Concretely, Morpho will rely less and less on the pool, but the latter would still be a supplier/borrower of last resort. you can think of chrysalis as an order book of finite size, closed by the underlying liquidity pool.

```

The Morpho team has of course put consideration into the same liquidity risks and scaling constraints. Allowing active market making within their book means lenders could demand as high a rate as needed to attract "replacement liquidity" and allow themselves to exit, or encourage borrowers to repay.

```
Assuming, that blockchain technology will be mature and efficient enough to address a larger audience, Morpho would rely less and less on PLFs. At some point, one can assume that it will not need an underlying PLF anymore and thus can stop relying on the liquidity pools, choosing its own course.
Under that incarnation, Morpho will probably appear as a more general kind of order book where every proposed position is connected in an instant, automatic and efficient way.
```

![](always_has_been.jpeg)

Directional orders rather than static or bidirectional pools are the future. The Volt Protocol swaps module designs draw on the same line of thought, where instead of paying DEX swap fees when exchanging stablecoins, VCON holders will market make and continue generating yield while waiting for their orders to fill.