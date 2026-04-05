# Pallets Integration

### This is first thing that needs to be done

## In cargo.toml (Choose any suitable version for the pallets)

```toml
ismp = { version = "=1.2.0", default-features = false }
pallet-ismp = { version = "=2503.1.0", default-features = false }
ismp-parachain = { version = "=2503.1.0", default-features = false } # This is only for parachain
ismp-grandpa = { version = "=2503.1.0", default-features = false }
pallet-ismp-runtime-api = { version = "=2503.1.0", default-features = false }
pallet-hyperbridge = { version = "=2503.1.0", default-features = false }
pallet-token-gateway = { version = "=2503.1.0", default-features = false }
```

## Add the configurations to your runtime

Add the ismp pallet configuration

```rust
parameter_types! {
    // For example, the hyperbridge parachain on Polkadot
    pub const Coprocessor: Option<StateMachine> = Some(StateMachine::Polkadot(3367));
    // pub const HostStateMachine: StateMachine = StateMachine::Polkadot(1000); // polkadot (For parachain)
    // pub const HostStateMachine: StateMachine = StateMachine::Kusama(1000); // kusama (For parachain)
    pub const HostStateMachine: StateMachine = StateMachine::Substrate(*b"MYID"); // solochain (You can add any 4 chars)
    // Here MYID is the state machine id
}

impl pallet_ismp::Config for Runtime {
    type RuntimeEvent = RuntimeEvent;
    type AdminOrigin = EnsureRoot<AccountId>; // You can add council here as well
    type HostStateMachine = HostStateMachine;
    type TimestampProvider = Timestamp;
    type Currency = Balances;
    type Balance = Balance;
    type Router = Router;
    type Coprocessor = Coprocessor;
    // Supported consensus clients
	//type ConsensusClients = (ismp_parachain::ParachainConsensusClient<Runtime, IsmpParachain>,);
	type ConsensusClients = (
        ismp_grandpa::consensus::GrandpaConsensusClient<Runtime>,
    );
    type OffchainDB = ();
    type FeeHandler = WeightFeeHandler<()>;
}

#[derive(Default)]
pub struct Router;
impl IsmpRouter for Router {
    fn module_for_id(&self, id: Vec<u8>) -> Result<Box<dyn IsmpModule>, anyhow::Error> {
        match id.as_slice() {
            pallet_hyperbridge::PALLET_HYPERBRIDGE_ID => {
                Ok(Box::new(pallet_hyperbridge::Pallet::<Runtime>::default()))
            }
            id if TokenGateway::is_token_gateway(id) => {
                Ok(Box::new(pallet_token_gateway::Pallet::<Runtime>::default()))
            }
            _ => Err(IsmpError::ModuleNotFound(id))?,
        }
    }
}
```

Add the pallet token gateway configuration

```rust
// Should provide an account that is funded and can be used to pay for asset creation
pub struct AssetAdmin;

impl Get<AccountId> for AssetAdmin {
	fn get() -> AccountId {
		Treasury::account_id()
	}
}

impl pallet_token_gateway::Config for Runtime {
    type RuntimeEvent = RuntimeEvent;
    type Dispatcher = Hyperbridge;
    type NativeCurrency = Balances;
    type AssetAdmin = AssetAdmin;
    type CreateOrigin = EnsureRoot<AccountId>;
    type Assets = Assets;
    type NativeAssetId = NativeAssetId;
    type Decimals = Decimals;
    type EvmToSubstrate = ();
    type WeightInfo = ();
}
```

Add the pallet ismp grandpa configuration

```rust
impl ismp_grandpa::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type IsmpHost = Ismp;
	type WeightInfo = ismp_grandpa_weight::WeightInfo<Runtime>;
	type RootOrigin = EnsureRoot<AccountId>; // Can add council as well
}
```

Add the pallet hyperbridge configuration

```rust
impl pallet_hyperbridge::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type IsmpHost = Ismp;
}
```

Add pallet ismp runtime apis

```rust
impl pallet_ismp_runtime_api::IsmpRuntimeApi<Block, <Block as BlockT>::Hash> for Runtime {
    fn host_state_machine() -> StateMachine {
        <Runtime as pallet_ismp::Config>::HostStateMachine::get()
    }

    fn challenge_period(state_machine_id: StateMachineId) -> Option<u64> {
        pallet_ismp::Pallet::<Runtime>::challenge_period(state_machine_id)
    }

    /// Fetch all ISMP events in the block, should only be called from runtime-api.
    fn block_events() -> Vec<::ismp::events::Event> {
        pallet_ismp::Pallet::<Runtime>::block_events()
    }

    /// Fetch all ISMP events and their extrinsic metadata, should only be called from runtime-api.
    fn block_events_with_metadata() -> Vec<(::ismp::events::Event, Option<u32>)> {
        pallet_ismp::Pallet::<Runtime>::block_events_with_metadata()
    }

    /// Return the scale encoded consensus state
    fn consensus_state(id: ConsensusClientId) -> Option<Vec<u8>> {
        pallet_ismp::Pallet::<Runtime>::consensus_states(id)
    }

    /// Return the timestamp this client was last updated in seconds
    fn state_machine_update_time(height: StateMachineHeight) -> Option<u64> {
        pallet_ismp::Pallet::<Runtime>::state_machine_update_time(height)
    }

    /// Return the latest height of the state machine
    fn latest_state_machine_height(id: StateMachineId) -> Option<u64> {
        pallet_ismp::Pallet::<Runtime>::latest_state_machine_height(id)
    }

    /// Get actual requests
    fn requests(commitments: Vec<H256>) -> Vec<Request> {
        pallet_ismp::Pallet::<Runtime>::requests(commitments)
    }

    /// Get actual requests
    fn responses(commitments: Vec<H256>) -> Vec<Response> {
        pallet_ismp::Pallet::<Runtime>::responses(commitments)
    }
}
```
