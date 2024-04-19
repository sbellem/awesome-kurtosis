## Ethereum dApp and Smart Contract Development Example

This example demonstrates how Kurtosis can be used for local dApp and Smart Contract development
by using the [`eth-network-package`](https://github.com/kurtosis-tech/eth-network-package) as a local Ethereum network. 
The `eth-network package` can be used as a low overhead, configurable, and composable alternative to frameworks like
`hardhat-network`, `ganache` and `anvil`. Kurtosis provides the developer more control and flexibility over the testnet they are using - a large reason why the [Ethereum Foundation used Kurtosis to test the Merge](https://www.kurtosis.com/blog/testing-the-ethereum-merge), and why they continue to use Kurtosis for various tests for upcoming upgrades to the network.

While this example covers only the Ethereum blockchain, Kurtosis can be used to locally configure and instantiate other blockchains (e.g. [NEAR](https://docs.near.org/develop/testing/kurtosis-localnet), [Avalanche](https://medium.com/avalancheavax/introducing-kurtosis-a-complete-testing-platform-to-accelerate-development-on-avalanche-6ad7e1147791) and allows you to connect your local testnet with any containerized service you wish.

### Setup

This folder contains a typical setup for a dApp developer using the
[Hardhat](https://hardhat.org/) framework. Let's explore it's contents.
- `contracts/` contains a few simple smart contracts for a Blackjack dApp 
- `scripts/` contains a script to deploy a token contract to our local Ethereum network
- `test/` contains a simple test for our token contract
- `hardhat.config.ts` configures our Hardhat setup. 
It allows us to configure Hardhat to use a local Ethereum network created by the `eth-network-package`.

### Running the Example

This assumes you have the following services installed:
- [Docker](https://docs.kurtosis.com/install#i-install--start-docker) with the Docker daemon running
- [Kurtosis CLI](https://docs.kurtosis.com/cli/)


1. To setup the dApp environment, simply run
    ```bash
    git clone --branch dev https://github.com/sbellem/awesome-kurtosis.git
    cd awesome-kurtosis/smart-contract-example
    make build
    ```
    This will clone the repository and build a docker image.

2. Now, Run
    ```
	make start
    ```
    The output should look something like this
    ```
	========================================== User Services ==========================================
	UUID           Name                                             Ports                                         Status
	dd3426e82fec   beacon-metrics-gazer                             http: 8080/tcp -> http://127.0.0.1:32827      RUNNING
	8bc8f2af64b1   blob-spammer                                     <none>                                        RUNNING
	850073956a7f   cl-1-lighthouse-geth                             http: 4000/tcp -> http://127.0.0.1:32824      RUNNING
	                                                                metrics: 5054/tcp -> http://127.0.0.1:32823   
	                                                                tcp-discovery: 9000/tcp -> 127.0.0.1:32822    
	                                                                udp-discovery: 9000/udp -> 127.0.0.1:32775    
	8c895f97042d   dora                                             http: 8080/tcp -> http://127.0.0.1:32828      RUNNING
	9903eeaa04d2   el-1-geth-lighthouse                             engine-rpc: 8551/tcp -> 127.0.0.1:32819       RUNNING
	                                                                metrics: 9001/tcp -> 127.0.0.1:32818          
	                                                                rpc: 8545/tcp -> http://127.0.0.1:32821       
	                                                                tcp-discovery: 30303/tcp -> 127.0.0.1:32817   
	                                                                udp-discovery: 30303/udp -> 127.0.0.1:32774   
	                                                                ws: 8546/tcp -> 127.0.0.1:32820               
	64f9ad180eab   vc-1-lighthouse-geth                             metrics: 8080/tcp -> http://127.0.0.1:32825   RUNNING
    ```
    We see a single node with a geth EL client and lighthouse CL client running has been created. The CL and EL client pair can be configured using a `.json` file. Currently, the Ethereum package supports lighthouse, nimbus, lodestar, teku, and prysm CL clients as well as the erigon, nethermind, besu, and geth EL clients. Read [here](https://github.com/kurtosis-tech/eth-network-package#configuring-the-network) to learn more. 
    Each EL and CL client requires data that differs per client, so we leverage the `prelaunch-data-generator`, built off this [Docker image](https://github.com/ethpandaops/ethereum-genesis-generator), to create the necessary genesis files and secrets.

3. Set environment variable `EL_RPC_PORT` to the port of the rpc uri output from any `el-<num>-<el_type>` service (e.g. `el-1-geth-lighthouse`). In this case, the port would be `32821`.
    ```bash
	export EL_RPC_PORT=32821
    ```

	OR in write to a `.env` file:

	```bash
	echo "EL_RPC_PORT=32821" > .env
	```

4. Run
    ```bash
	make check-balances
    ```
    This verifies that network is working and detects the prefunded accounts on the network, created by the `eth-network-package`.
    The output should look something like this:
    ```bash
    0x878705ba3f8Bc32FCf7F4CAa1A35E72AF65CF766 has balance 10000000000000000000000000
    0x4E9A3d9D1cd2A2b2371b8b3F489aE72259886f1A has balance 10000000000000000000000000
    0xdF8466f277964Bb7a0FFD819403302C34DCD530A has balance 10000000000000000000000000
    0x5c613e39Fc0Ad91AfDA24587e6f52192d75FBA50 has balance 10000000000000000000000000
    0x375ae6107f8cC4cF34842B71C6F746a362Ad8EAc has balance 10000000000000000000000000
    0x1F6298457C5d76270325B724Da5d1953923a6B88 has balance 10000000000000000000000000
    ```
5. Now, we can run dev/test workflows against our network! For example, let's deploy the `ChipToken` so we can iterate and test how things work locally:
    ```bash
	make deploy
    ```
    The output should look something like this
    ```bash
    ChipToken deployed to: 0xAb2A01BC351770D09611Ac80f1DE076D56E0487d
    ```

6. Test:

	```bash
	make tests
	```