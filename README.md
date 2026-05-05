# base221zimport time
from web3 import Web3

RPC_URL = "https://mainnet.base.org"

# Uniswap V2 Swap event signature
SWAP_TOPIC = Web3.keccak(
    text="Swap(address,uint256,uint256,uint256,uint256,address)"
).hex()


def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))

    if not w3.is_connected():
        raise RuntimeError("Cannot connect to Base RPC")

    print("Connected to Base")
    print("Monitoring DEX swap activity...\n")

    last_block = w3.eth.block_number

    while True:
        try:
            current_block = w3.eth.block_number

            if current_block > last_block:

                logs = w3.eth.get_logs({
                    "fromBlock": last_block + 1,
                    "toBlock": current_block,
                    "topics": [SWAP_TOPIC]
                })

                for log in logs:
                    print("🔄 Swap Detected")
                    print("Pool/Contract:", log["address"])
                    print("Tx:", log["transactionHash"].hex())
                    print("Block:", log["blockNumber"])
                    print()

                last_block = current_block

            time.sleep(2)

        except Exception as e:
            print("Error:", e)
            time.sleep(5)


if __name__ == "__main__":
    main()
