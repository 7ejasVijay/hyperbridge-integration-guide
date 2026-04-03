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
    // The host state machine of this pallet, your state machine id goes here
    pub const HostStateMachine: StateMachine = StateMachine::Polkadot(1000); // polkadot
    // pub const HostStateMachine: StateMachine = StateMachine::Kusama(1000); // kusama
    // pub const HostStateMachine: StateMachine = StateMachine::Substrate(*b"MYID"); // solochain (You can add any 4 letter chars here)
}

impl pallet_ismp::Config for Runtime {
    // configure the runtime event
    type RuntimeEvent = RuntimeEvent;
    // Permissioned origin who can create or update consensus clients
    type AdminOrigin = EnsureRoot<AccountId>; // You can add council here as well
    // The state machine identifier for this state machine
    type HostStateMachine = HostStateMachine;
    // The pallet_timestamp pallet
    type TimestampProvider = Timestamp;
    // The currency implementation that is offered to relayers
    type Currency = Balances;
    // The balance type for the currency implementation
    type Balance = Balance;
    // Router implementation for routing requests/responses to their respective modules
    type Router = Router;
    // Optional coprocessor for incoming requests/responses
    type Coprocessor = Coprocessor;
    // Supported consensus clients
	  //type ConsensusClients = (ismp_parachain::ParachainConsensusClient<Runtime, IsmpParachain>,);
	  type ConsensusClients = (
        ismp_grandpa::consensus::GrandpaConsensusClient<Runtime>,
    );
    // Offchain database implementation. Outgoing requests and responses are
    // inserted in this database, while their commitments are stored onchain.
    type OffchainDB = ();
    // The fee handler implementation
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
