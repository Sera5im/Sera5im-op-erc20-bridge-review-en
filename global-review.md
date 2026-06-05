# Global Review

This review fixes the system-level guarantees that connect deposit, withdraw, and the surrounding config surface into one bridge security model.

## Global Invariants

- the bridge system must preserve separation between `original-token accounting` and `representation-token accounting`
- standard ERC20 `deposit` and `withdraw` must remain mirror lifecycle paths
- cross-domain ERC20 completion must happen only through `bridge -> counterpart bridge` semantics
- `local/remote token` semantics must remain relative to the current chain and must reverse correctly when moving between sides
- the bridge system must not allow asset credit or release without a correct source-side accounting step
- the `paused/config layer` must remain able to stop the finalize completion path
