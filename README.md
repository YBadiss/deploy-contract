# Deploy EVM smart contract

Suite of actions to deploy smart contracts using [forge](https://github.com/foundry-rs/foundry/tree/master/crates/forge).

You can use it to either do a simple contract deployment, or a proxied contract.

You can specify the target EVM to use, and can also trigger the verification of the contract on Etherscan.

The action generates a report of the deployment and verification, then adds the ABI of the contract as an artifact on your action run.

![Report example](./report-example.png)

# Examples

## Deploying a simple ownable contract

```yaml
name: Deploy main contract
on:
  push:
    branches: [ main ]
jobs:
  deploy-contract:
    runs-on: ubuntu-latest
    permissions: write-all
    steps:
      - uses: actions/checkout@v4
      - uses: YBadiss/deploy-contract@v1
        with:
          contract-name: 'OwnableContractName'
          parent-dir: './path/to/contracts'
          constructor-args: '${{ vars.OWNER_ADDRESS }}'
          deployer-pk: ${{ secrets.DEPLOYER_PK }}
          chain-id: '11155111'
          rpc-url: ${{ secrets.RPC_ETH_SEPOLIA_HTTPS }}
          verify: true
          etherscan-url: 'https://sepolia.etherscan.io'
          etherscan-api-key: ${{ secrets.ETHERSCAN_SEPOLIA_API_KEY }}
```

## Deploying a proxy and its implementation

```yaml
name: Deploy proxied contract
on:
  push:
    branches: [ main ]
jobs:
  deploy-proxied-contract:
    runs-on: ubuntu-latest
    permissions: write-all
    steps:
      - uses: actions/checkout@v4
      - name: Deploy an implementation and its proxy
        uses: YBadiss/deploy-contract/proxy@v1
        id: deploy
        with:
          implementation-contract-name: 'ImplementationContractName'
          proxy-contract-name: 'ProxyContractName'
          call-args: '"initialize(address)" "${{ vars.OWNER_ADDRESS }}"'
          parent-dir: './path/to/contracts'
          deployer-pk: ${{ secrets.DEPLOYER_PK }}
          chain-id: '11155111'
          rpc-url: ${{ secrets.RPC_ETH_SEPOLIA_HTTPS }}
          verify: true
          etherscan-url: 'https://sepolia.etherscan.io'
          etherscan-api-key: ${{ secrets.ETHERSCAN_SEPOLIA_API_KEY }}
      - name: Upgrade to a new implementation
        uses: YBadiss/deploy-contract/proxy@v1
        id: upgrade
        with:
          implementation-contract-name: 'NewImplementationContractName'
          proxy-contract-address: ${{ steps.deploy.outputs.proxy-contract-address }}
          call-args: ''
          parent-dir: './path/to/contracts'
          deployer-pk: ${{ secrets.DEPLOYER_PK }}
          chain-id: '11155111'
          rpc-url: ${{ secrets.RPC_ETH_SEPOLIA_HTTPS }}
          verify: true
          etherscan-url: 'https://sepolia.etherscan.io'
          etherscan-api-key: ${{ secrets.ETHERSCAN_SEPOLIA_API_KEY }}
```


# Dependencies and requirements

- You must have checked out the code using the [standard checkout action](https://github.com/actions/checkout).
- Installing the latest version of `forge` is performed using the action [foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain).
- Your code must be in a `src` folder, under the [`parent-dir` input](#parent-dir).

# deploy-contract

## Inputs

### contract-name

Name of the contract to deploy.

Must be the name of the main sol file, and of the target constract class.

- `REQUIRED`

### chain-id

ID of the chain to deploy to. If in doubt see https://chainlist.org/.

- `REQUIRED`

### rpc-url

URL of the RPC to use to deploy the contract.

- `REQUIRED`
- :warning: Use github secrets if the URL embeds an API Key.

### deployer-pk

Private key to use for deployment, in hex format.

- `REQUIRED`
- :warning: Use github secrets.

### parent-dir

Parent directory of `src` and `out`.

- `OPTIONAL, DEFAULT="."`

### constructor-args

Arguments to pass to the contract's constructor.

- `OPTIONAL, DEFAULT=""`

### verify

Whether to verify the contract or not on Etherscan.
If set to `true`, [etherscan-url](#etherscan-url) and [etherscan-api-key](#etherscan-api-key) are required.

- `OPTIONAL, DEFAULT="false"`

### etherscan-url

URL of the etherscan endpoint to use for verification and reporting.

- `OPTIONAL, DEFAULT=""`

### etherscan-api-key

API key used for verification.

- `OPTIONAL, DEFAULT=""`
- :warning: Use github secrets.

## Outputs

### contract-address

Address of the newly deployed contract.

### transaction-hash

Hash of the deployment transaction.

# deploy-contract/proxy

## Inputs

### implementation-contract-name

Name of the implementation to deploy.

Must be the name of the main sol file, and of the target constract class.

- `REQUIRED`

### proxy-contract-name

Name of the proxy to deploy.

Must be the name of the main sol file, and of the target constract class.

- `OPTIONAL, DEFAULT=""`

### proxy-contract-address

Address of the proxy contract.

- `OPTIONAL, DEFAULT=""`

### call-args

Arguments to initialise the implementation contract with, or pass to upgradeAndCall.

- In the case of a new deployment, provide the initialiser's function and args.
- In the case of an upgrade, provide the optional call's function and args.

- `OPTIONAL, DEFAULT=""`

### chain-id

ID of the chain to deploy to. If in doubt see https://chainlist.org/.

- `REQUIRED`

### rpc-url

URL of the RPC to use to deploy the contract.

- `REQUIRED`
- :warning: Use github secrets if the URL embeds an API Key.

### deployer-pk

Private key to use for deployment, in hex format.

- `REQUIRED`
- :warning: Use github secrets.

### parent-dir

Parent directory of `src` and `out`.

- `OPTIONAL, DEFAULT="."`

### verify

Whether to verify the contract or not on Etherscan.
If set to `true`, [etherscan-url](#etherscan-url) and [etherscan-api-key](#etherscan-api-key) are required.

- `OPTIONAL, DEFAULT="false"`

### etherscan-url

URL of the etherscan endpoint to use for verification and reporting.

- `OPTIONAL, DEFAULT=""`

### etherscan-api-key

API key used for verification.

- `OPTIONAL, DEFAULT=""`
- :warning: Use github secrets.

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
