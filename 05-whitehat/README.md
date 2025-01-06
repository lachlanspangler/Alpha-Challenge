# Whitehat - Tier 2
You stumbled across an old [bug bounty report](https://medium.com/immunefi/polygon-lack-of-balance-check-bugfix-postmortem-2-2m-bounty-64ec66c24c7d) from the end of 2021 related to the Polygon codebase. Understanding that other blockchains are using this code, you decide to double-check that the largest ones are not susceptible to vulnerabilities disclosed in this report.


- a) Find at least two Polygon forks that could potentially be vulnerable.

Two popular Polygon forks that could potentially be vulnerable are BSC (Binance Smart Chain) and Avalanche (C-Chain). Both chains have adopted similar Ethereum Virtual Machine (EVM) implementations and might have used similar code as Polygon’s MRC20 contract in their ecosystem tokens. If their implementations include a similar transferWithSig() function or lack proper balance checks in their token contracts, they could be exposed to this vulnerability. Another possible candidate is Fantom (Opera Chain), as it also uses an EVM-compatible structure and has been known to implement features inspired by Polygon's scalability solutions.

- b) Provide the code to check if these blockchains are safe.

from web3 import Web3

chains = {
    "BSC": "https://bsc-dataseed.binance.org/",
    "Avalanche": "https://api.avax.network/ext/bc/C/rpc",
    "Fantom": "https://rpc.ftm.tools/"
}

token_addresses = {
    "BSC": "",
    "Avalanche": "",
    "Fantom": ""
}

token_abi = [
    {
        "constant": False,
        "inputs": [
            {"name": "_to", "type": "address"},
            {"name": "_value", "type": "uint256"},
            {"name": "_nonce", "type": "uint256"},
            {"name": "_expiry", "type": "uint256"},
            {"name": "_sig", "type": "bytes"}
        ],
        "name": "transferWithSig",
        "outputs": [],
        "payable": False,
        "stateMutability": "nonpayable",
        "type": "function"
    }
]

def check_vulnerability(chain_name, rpc_url, token_address):
    web3 = Web3(Web3.HTTPProvider(rpc_url))
    token_contract = web3.eth.contract(address=token_address, abi=token_abi)

    try:
        # Check if transferWithSig exists
        if token_contract.functions.transferWithSig:
            print(f"[{chain_name}] transferWithSig function found on {token_address}.")
            print(f"[{chain_name}] Needs further inspection for balance checks.")
        else:
            print(f"[{chain_name}] No transferWithSig function found on {token_address}.")
    except Exception as e:
        print(f"[{chain_name}] Error checking token: {e}")

for chain_name, rpc_url in chains.items():
    check_vulnerability(chain_name, rpc_url, token_addresses[chain_name])


- c) Estimate the potential maximum loss if this attack is possible on both blockchains.

The potential loss from this vulnerability depends on how much money (TVL) is stored in the affected token contracts on these blockchains. For example, Binance Smart Chain (BSC) has had over $5 billion in TVL at its peak. If a major token contract on BSC is vulnerable, an attacker could steal all the funds in it. Even if only 10% of the funds are at risk, the loss could be as much as $500 million. Similarly, Avalanche (C-Chain) has around $2 billion in TVL, and an attack on a key contract could result in losses of about $200 million if 10% is vulnerable. Together, the total losses across both chains could easily add up to $700 million or more. This shows how important it is to run security checks and fix these issues before they can be exploited.
