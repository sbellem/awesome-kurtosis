.DEFAULT_GOAL := help

##@
##@ Kurtosis local network management commands
##@
start: ##@ Start local network
	kurtosis run --enclave local-eth-testnet --image-download always github.com/kurtosis-tech/ethereum-package

stop: ##@ Stop local network
	kurtosis enclave stop local-eth-testnet

inspect: ##@ Inspect kurtosis enclave for local network
	kurtosis enclave inspect local-eth-testnet
	


##@
##@ Hardhat commands for the ChipToken contract
##@
check-balances: ##@ Verify that the network is working and detect the prefunded accounts on the network
	docker compose run --rm blackjack npx hardhat balances --network localnet

deploy: ##@ Deploy ChipToken contract
	docker compose run --rm blackjack npx hardhat run scripts/deploy.ts --network localnet

tests: ##@ Test ChipToken contract
	docker compose run --rm blackjack npx hardhat test --network localnet


##@
##@ Other commands
##@
# Thanks to https://gist.github.com/BlackHole1 for their example at
# https://gist.github.com/prwhite/8168133?permalink_comment_id=4718682#gistcomment-4718682
help: ##@ (Default) Print listing of key targets with their descriptions
	@printf "\033[33m\nEthereum dApp and Smart Contract Development Example\n"
	@printf "\n\033[32mUsage: make <command>\033[0m\n"
	@grep -F -h "##@" $(MAKEFILE_LIST) | grep -F -v grep -F | sed -e 's/\\$$//' | awk 'BEGIN {FS = ":*[[:space:]]*##@[[:space:]]*"}; \
	{ \
		if($$2 == "") \
			pass; \
		else if($$0 ~ /^#/) \
			printf "\n%s\n", $$2; \
		else if($$1 == "") \
			printf "     %-20s%s\n", "", $$2; \
		else \
			printf "    \033[34m%-27s\033[0m %s\n", $$1, $$2; \
	}'

build: ##@ Build docker image used to run the local network and the attacks
	docker compose build

clean: ##@ Destroy the local-eth-testnet Kurtosis enclave, even if it's running
	kurtosis enclave rm --force local-eth-testnet
