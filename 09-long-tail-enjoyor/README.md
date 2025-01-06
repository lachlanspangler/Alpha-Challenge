# Long-Tail-Enjoyor - Tier 3
You’re an active member of the Synthetix community and noticed that the implementation of one of [their latest SIPs](https://sips.synthetix.io/sips/sip-112/) will be deployed today (May 13, 2021).


- a) How, in theory, can you make money based on the SIP specification?

You can make money by taking advantage of price differences between sETH (Synthetix ETH) and regular ETH. The EtherWrapper contract allows you to deposit ETH or WETH and mint sETH for a small fee. If sETH is trading at a higher price than ETH on exchanges like Curve, you can mint sETH, sell it for ETH at the higher price, and make a profit after fees. Similarly, if sETH is trading below ETH, you can buy sETH cheaply, burn it in the contract to redeem ETH or WETH, and profit from the price difference. This creates an opportunity to make money whenever sETH and ETH prices are not equal.

- b) Provide the simulation data when you execute the opportunity using the full maxETH amount.


If you deposit the maximum allowed amount of 5,000 ETH into the EtherWrapper contract, you would receive 4,997.5 sETH after paying a small minting fee of 0.05%. If sETH is trading at a 2% premium to ETH on an exchange like Curve, you can sell the 4,997.5 sETH for 5,097.45 ETH. Subtracting your initial deposit of 5,000 ETH, you make a profit of 97.45 ETH.
