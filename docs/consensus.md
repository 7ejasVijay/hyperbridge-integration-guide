# Consensus Relayer

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
[polkadex]
type = "grandpa"

[polkadex.substrate]
# Solochains's websocket RPC
# Your chain's wss rpc url
rpc_ws = ""
# Hashing can be "Keccak" or "Blake2"
hashing = "Blake2"
# Solochains's consensus state id on Hyperbridge
# should be 4 utf-8 chars chosen by solochain
consensus_state_id = "MYID"
# Solochains's state machine id. eg
state_machine = "SUBSTRATE-MYID"
# Relayer account seed (Private Key of your substrate wallet)
signer = ""

[polkadex.grandpa]
# Solochains's websocket RPC
# Your chain's wss rpc url
rpc = ""
# Solochains's slot duration
slot_duration = 12000
# How frequently to exchange consensus proofs
consensus_update_frequency = 60
# Any para ids to prove if solochain is actually a relay chain
para_ids = []

[relayer]
maximum_update_intervals = []
```
