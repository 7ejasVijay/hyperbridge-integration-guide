# Consensus Relayer

> A consensus relayer is responsible for exchanging finality proofs of cross-chain messages.

You have already add the `ismp-grandpa` to your runtime

### Whitelisting State Machines

The ismp-grandpa pallet requires state machines to be whitelisted before any consensus proofs can be accepted. This ensures that solochains are not spammed with consensus proofs of unwanted chains.

![Adding State Machines](https://docs.hyperbridge.network/add_state_machine.png)

### Configuring the relayer

Pull the image of the consensus relayer

```shell
$ docker pull polytopelabs/tesseract-consensus:latest
```

Copy the consensus configuration and add the necessary creds so that the container can run with this toml

```toml
# Required
[hyperbridge]
type = "grandpa"

[hyperbridge.grandpa]
# Hyperbridge's relay chain websocket RPC
rpc = "wss://paseo-rpc.n.dwellir.com"
# Hyperbridge's slot duration
# on Polkadot
# slot_duration = 12000
# on Paseo
slot_duration = 6000
# How frequently to exchange consensus proofs
consensus_update_frequency = 60
# Hyperbridge's paraId on the provided relay chain
# For Paseo Testnet: para_ids = [4009]
# For Polkadot Mainnet: para_ids = [3367]
para_ids = [4009]

[hyperbridge.substrate]
# Hyperbridge's relay chain websocket RPC
rpc_ws = "wss://hyperbridge-paseo-rpc.blockops.network"
# Hyperbridge's hashing algorithm
hashing = "Keccak"
# Hyperbridge's consensus state id
# For Paseo Testnet: PAS0
# For Polkadot Mainnet: DOT0
consensus_state_id = "PAS0"
# Hyperbridge's state machine ID
# For Paseo Testnet: KUSAMA-4009
# For Polkadot Mainnet: POLKADOT-3367
state_machine = "KUSAMA-4009"
# Relayer account seed (Private Key of your substrate wallet)
signer = ""

# can use any key here
[YourSolochain]
type = "grandpa"

[YourSolochain.substrate]
# Solochains's websocket RPC
rpc_ws = "<Your chain's wss rpc url>"
# Hashing can be "Keccak" or "Blake2"
hashing = "Blake2"
# Solochains's consensus state id on Hyperbridge
# should be 4 utf-8 chars chosen by solochain
consensus_state_id = "MYID"
# Solochains's state machine id. eg
state_machine = "SUBSTRATE-MYID"
# Relayer account seed (Private Key of your substrate wallet)
signer = ""

[YourSolochain.grandpa]
# Solochains's websocket RPC
rpc = "<Your chain's wss rpc url>"
# Solochains's slot duration
slot_duration = 12000
# How frequently to exchange consensus proofs
consensus_update_frequency = 60
# Any para ids to prove if solochain is actually a relay chain
para_ids = []

[relayer]
maximum_update_intervals = []
```

### Consensus State Initialization

Before running the relayer, you must first initialize Hyperbridge's consensus state on your solochain.

```shell
$ docker run \
    --network=host \
    -v /path/to/consensus.toml:/root/consensus.toml \
    polytopelabs/tesseract-consensus:latest \
    --config=/root/consensus.toml \
    log-consensus-state KUSAMA-4009
```

This should print out a potentially long hex string. Next you'll use this hex string to initialize the Hyperbridge consensus state on your solochain through an extrinsic as shown below.

![Adding State Machines](https://docs.hyperbridge.network/grnp_init.png)

Once this is completed on your solochain, the same will need to also be executed on Hyperbridge's side. You will need to get the consensus state from your chain using the following command.

```shell
$ docker run \
    --network=host \
    -v /path/to/consensus.toml:/root/consensus.toml \
    polytopelabs/tesseract-consensus:latest \
    --config=/root/consensus.toml \
    log-consensus-state SUBSTRATE-MYID
```

> You will need to share the consensus state of your chain with the hyperbridge team by [creating an issue on github](https://github.com/polytope-labs/hyperbridge/issues/new).

Reference: [Consensus document for hyperbridge](https://docs.hyperbridge.network/developers/polkadot/solochains/)
