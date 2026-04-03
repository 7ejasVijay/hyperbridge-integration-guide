# Pallets Integration

### This is first thing that needs to be done

In cargo.toml (Choose any suitable version for the pallets)

```toml
ismp = { version = "=1.2.0", default-features = false }
pallet-ismp = { version = "=2503.1.0", default-features = false }
ismp-parachain = { version = "=2503.1.0", default-features = false } # This is only for parachain
ismp-grandpa = { version = "=2503.1.0", default-features = false }
pallet-ismp-runtime-api = { version = "=2503.1.0", default-features = false }
pallet-hyperbridge = { version = "=2503.1.0", default-features = false }
pallet-token-gateway = { version = "=2503.1.0", default-features = false }
```
