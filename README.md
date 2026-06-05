# Optimism Standard Bridge ERC20 Review

This repository contains my review notes on the Optimism Standard Bridge ERC20 paths.

## What This Review Covers

The Optimism Standard Bridge moves ERC20 assets between L1 and L2 through a bridge/messenger model:

- on deposit, the original L1 token is locked and the L2 representation is minted
- on withdrawal, the L2 representation is burned and the locked L1 token is released

This review is focused on the ERC20 deposit and withdrawal paths at the bridge contract / application layer.

## Scope

My current scope here is:

- L1StandardBridge deposit logic
- L2StandardBridge withdrawal initiation logic
- shared StandardBridge ERC20 bridge logic
- L1 and L2 finalize semantics for the standard ERC20 path
- surrounding config, init, pause, and upgrade surface

My current scope here is focused on the bridge application layer rather than a deeper transport-layer review of messenger, portal, or proving internals. Because of that, components such as `messenger.sendMessage(...)`, the messenger contracts, the portal, and the proving/relay execution path are treated here only as transport boundaries between the reviewed L1 and L2 contract-layer paths.

## Review Structure

- [deposit-review.md](deposit-review.md)
  ERC20 deposit path review from L1 entrypoints and source-side accounting to L2 final credit.

- [withdraw-review.md](withdraw-review.md)
  ERC20 withdrawal path review from the L2 withdraw entrypoint and burn path to L1 final release.

- [out-of-flow-review.md](out-of-flow-review.md)
  Review of the surrounding config, init, pause, wrapper, and upgrade-related surface.

- [global-review.md](global-review.md)
  System-level guarantees that connect deposit, withdrawal, and the surrounding admin/config layer.
