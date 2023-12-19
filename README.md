# Deploy smart contract

This action deploys a smart contract using [forge](https://github.com/foundry-rs/foundry/tree/master/crates/forge).

It depends on the action [foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain) to install the latest version of forge.

# Usage

```yaml
- uses: YBadiss/deploy-contract@v1
  with:
    # Name of the contract to deploy. Must be the name of the main sol file.
    contract-name: ''

    # Parent directory of `src` and `out`.
    #
    # Default: "."
    parent-dir: ''

    # ID of the chain to deploy to. If in doubt see https://chainlist.org/.
    chain-id: ''

    # URL of the RPC to use to deploy the contract.
    rpc-url: ''

    # Private key to use for deployment.
    deployer-pk: ''

    # Arguments to pass to the contract's constructor.
    #
    # Default: ""
    constructor-args: ''

    # Whether we must verify the contract or not on etherscan.
    # If set to `true`, `etherscan-url` and `etherscan-api-key` are required.
    #
    # Default: "false"
    verify: ''

    # URL of the etherscan endpoint to use for verification and reporting.
    #
    # Default: ""
    etherscan-url: ''

    # API key used for verification.
    #
    # Default: ""
    etherscan-api-key: ''
```

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE)