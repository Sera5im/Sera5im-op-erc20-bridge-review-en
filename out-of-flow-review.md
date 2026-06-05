# Out-of-Flow Review

This review covers the config, init, routing, pause, wrapper, and upgrade surface around the deposit/withdraw lifecycle. It is not about the normal asset path itself, but about the surrounding admin/config assumptions that keep that path valid.

## `L1StandardBridge.initialize(...)`

```solidity
function initialize(
    ICrossDomainMessenger _messenger,
    ISystemConfig _systemConfig
)
    external
    reinitializer(initVersion())
{
    // Initialization transactions must come from the ProxyAdmin or its owner.
    _assertOnlyProxyAdminOrProxyAdminOwner();

    // Now perform initialization logic.
    systemConfig = _systemConfig;
    __StandardBridge_init({
        _messenger: _messenger,
        _otherBridge: StandardBridge(payable(Predeploys.L2_STANDARD_BRIDGE))
    });
}
```

What it does:

- initializes the bridge only for a ProxyAdmin-authorized caller
- binds `systemConfig`
- calls the base bridge init
- sets the `messenger` and counterpart `L2 bridge`

Invariants:

- the bridge initialization path must be restricted only to a ProxyAdmin-authorized caller
- `initialize(...)` must bind the `L1 bridge` to the correct local `systemConfig`
- `initialize(...)` must set the correct `messenger` for the base `StandardBridge`
- `initialize(...)` must set the correct counterpart `L2 bridge` for the base `StandardBridge`

## `L1StandardBridge.paused()`

```solidity
function paused() public view override returns (bool) {
    return systemConfig.paused();
}
```

What it does:

- reads the current pause state from `systemConfig`

Invariants:

- the bridge pause state must be read from the linked `systemConfig`, not from an arbitrary hidden source

## `L1StandardBridge.superchainConfig()`

```solidity
function superchainConfig() public view returns (ISuperchainConfig) {
    return systemConfig.superchainConfig();
}
```

What it does:

- returns `superchainConfig` through the already linked `systemConfig`

Invariants:

- `superchainConfig()` must resolve the superchain config through the already bound `systemConfig`

## `L1StandardBridge.l2TokenBridge()`

```solidity
function l2TokenBridge() external view returns (address) {
    return address(otherBridge);
}
```

What it does:

- exposes the address of the counterpart `L2 bridge`

Invariants:

- the public counterpart-bridge view must reflect the already configured `otherBridge`

## `L1StandardBridge.finalizeERC20Withdrawal(...)`

```solidity
function finalizeERC20Withdrawal(
    address _l1Token,
    address _l2Token,
    address _from,
    address _to,
    uint256 _amount,
    bytes calldata _extraData
)
    external
{
    finalizeBridgeERC20(_l1Token, _l2Token, _from, _to, _amount, _extraData);
}
```

What it does:

- acts as a legacy/public wrapper over `finalizeBridgeERC20(...)`

Invariants:

- `finalizeERC20Withdrawal(...)` must forward the withdrawal finalize semantics into `finalizeBridgeERC20(...)` without rewriting arguments

## `StandardBridge.__StandardBridge_init(...)`

```solidity
function __StandardBridge_init(
    ICrossDomainMessenger _messenger,
    StandardBridge _otherBridge
)
    internal
    onlyInitializing
{
    messenger = _messenger;
    otherBridge = _otherBridge;
}
```

What it does:

- binds the bridge to `messenger`
- binds the bridge to the counterpart bridge

Invariants:

- `__StandardBridge_init(...)` must bind the bridge to the correct cross-domain `messenger`
- `__StandardBridge_init(...)` must bind the bridge to the correct counterpart bridge
