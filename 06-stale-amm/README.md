# stale-amm - Tier 2
It’s May 2021, and while searching for new trading pools, you discovered that someone made [2.8x](https://etherscan.io/tx/0x3f1b5baef6ea7f622834eabe7634bf89e3f473b62a73e357fdd04a1a5cf32ecf) by selling TUSD through one of the old Uniswap v1 pools. Let’s figure out how it happened.


- a) What is the reason for the stale price in this pool?

The stale price in the Uniswap v1 TUSD pool happened because Uniswap v1 does not automatically update prices based on the market. Prices only change when someone makes a trade in the pool. If no one trades for a long time, the price in the pool can become outdated compared to the actual market price. This is likely what happened with TUSD in this pool, where the price stayed higher than the market price, allowing someone to sell TUSD at an inflated value and make a profit.

- b) Provide all necessary simulation data to arbitrage the pool on January 23, 2022.

To simulate an arbitrage opportunity on January 23, 2022, we would need data like the pool's TUSD and ETH balances (reserves) on that date and the true market price of TUSD. For example, if the pool had 500 ETH and 100,000 TUSD, the price in the pool would be 0.005 ETH per TUSD. If the market price was 0.01 ETH per TUSD, there would be an opportunity to buy TUSD at the lower market price and sell it in the pool for a profit.

- c) Could you execute the arbitrage on March 14, 2022? If not, explain why.


By March 14, 2022, arbitrage would no longer be possible because Uniswap v1 was mostly inactive by then. Newer versions like Uniswap v2 and v3 had taken over, and most liquidity and users had moved to these updated platforms. Even if the pool still existed, it likely had very little or no liquidity left, making it impossible to perform any meaningful trades or take advantage of price differences.
