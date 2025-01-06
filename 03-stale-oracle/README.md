# Stale-Oracle - Tier 1
While digging around, you learn about the manual process involved in updating oracle prices for [Compound v1](https://etherscan.io/address/0x3fda67f7583380e67ef93072294a7fac882fd7e7). According to the official blog post, the protocol was deprecated on June 3, 2019. However, according to the contract, it was never paused, and there are no functions for freezing markets. Given this, perhaps it’s possible to use stale prices and borrow all assets cheaply?


- a) Are the prices stale according to the view of Compound v1?

Yes, the prices in Compound v1 appear to be stale. Compound v1 relied on a manual process for updating oracle prices. This means that unless someone explicitly updated the prices, they could remain outdated. Since Compound v1 was deprecated on June 3, 2019, and the protocol wasn’t paused or frozen, it’s likely that prices weren’t updated after that date. This creates an opportunity for an attacker to exploit outdated (stale) prices, allowing them to borrow assets at artificially low costs if the prices didn’t reflect the true market value.

- b) Were markets paused in some way? Provide all necessary data to simulate the borrowing of any asset on June 5, 2019 to prove your point.

No, markets were not paused in Compound v1. According to the smart contract code, there were no built-in functions for freezing or pausing markets. As of June 5, 2019, the protocol was still functional despite being deprecated. This means that borrowing and lending operations could still occur, even though the price oracle might not have been updated.

To simulate borrowing, we can look at an example of how this might have been exploited. Suppose the oracle price for ETH remained outdated and undervalued on June 5, 2019. An attacker could:
Deposit collateral using an asset priced accurately.
Borrow ETH at the stale price, gaining significantly more ETH than they should be able to.
Sell the borrowed ETH at its true market price, profiting from the difference. An attacker could deposit $1,000 worth of USDC as collateral, borrow 10 ETH using the stale price of $100/ETH, and then sell it at the actual market price of $3,681/ETH for a massive profit of $35,810.
