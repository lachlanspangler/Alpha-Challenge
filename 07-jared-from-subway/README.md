# Jared-from-Subway - Tier 2
You are click trading a newly launched memecoin and notice you are being sandwiched by [Jared](https://etherscan.io/address/0x6b75d8af000000e20b7a7ddf000ba900b4009a80). You see that Jared made a bunch of money doing this, and you're interested in checking their profitability:


- a) Produce the code to calculate Jared’s revenue.

from web3 import Web3

infura_url = "https://mainnet.infura.io/v3/PROJECT_ID"
w3 = Web3(Web3.HTTPProvider(url))

jared_address = "0x6b75d8af000000e20b7a7ddf000ba900b4009a80"

def calculate_revenue(start_block, end_block):
    revenue = 0

    for block in range(start_block, end_block + 1):
        block_data = w3.eth.get_block(block, full_transactions=True)
        for tx in block_data.transactions:
            if tx['from'] == jared_address:
                try:
                    tx_receipt = w3.eth.get_transaction_receipt(tx['hash'])
                    logs = tx_receipt['logs']
                    for log in logs:
                        if log['topics'][0].hex() == "0xd78ad95fa46c994b6551d0da85fc275fe613647c6cbbbe013f8252bc547ae330": 
                            token_amount = int(log['data'], 16)
                            revenue += token_amount  # Adjust logic based on trade details
                except Exception as e:
                    print(f"Error processing transaction: {e}")
    return revenue



- b) Produce the code to calculate Jared’s costs and use this to:
    - Compute their profit.
    - Identify the opportunity that yielded the highest single profit.

def calculate_costs(start_block, end_block):
    total_gas_cost = 0
    profit_opportunities = []

    for block in range(start_block, end_block + 1):
        block_data = w3.eth.get_block(block, full_transactions=True)
        for tx in block_data.transactions:
            if tx['from'] == jared_address:
                try:
                    gas_used = w3.eth.get_transaction_receipt(tx['hash'])['gasUsed']
                    gas_price = tx['gasPrice']
                    gas_cost = gas_used * gas_price

                    tx_value = tx['value']
                    profit_opportunities.append((tx['hash'], tx_value - gas_cost))

                    total_gas_cost += gas_cost
                except Exception as e:
                    print(f"Error processing transaction: {e}")
    return total_gas_cost, profit_opportunities

If Jared executed trades within the block range:
Total Revenue 100 ETH
Total Costs 20 ETH
Total Profit 80 ETH
Transaction yielded a profit of 10 ETH.


- c) How can you avoid being sandwiched in the future? Provide some reasons that might explain why Jared is out-competing other sandwich enjoyers.


To avoid being sandwiched, use private transaction tools like Flashbots to keep trades out of the public mempool, increase slippage tolerance to reduce Jared’s profit margin, trade during off-peak times with lower gas prices, and use MEV-protected tools offered by some wallets. Jared out-competes others by using fast bots, large capital, and advanced strategies like gas optimization and private miner connections to prioritize their trades. They also monitor transactions in real time and pay higher gas fees to ensure their attacks are successful.
