## 1. Deposit can only work once in multicall

In the `validateDeposit()` function, the following code exists:

```solidity
if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {
    revert Errors.INVALID_MSG_VALUE(msg.value);
}
```

Note that this code will necessarily revert if there are two deposits with different amounts in one `multicall()` bundle. This may not be intended and this issue is avoidable with a different implementation.

## 2. `isMulticall` doesn't work in nested calls

Consider the following condensed implementation of `multicall()`:

```solidity
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
    state.data.isMulticall = true;

    results = new bytes[](data.length);
    for (uint256 i = 0; i < data.length; i++) {
        results[i] = Address.functionDelegateCall(address(this), data[i]);
    }

    state.data.isMulticall = false;
}
```

Note that `multicall()` is itself a function that can be invoked by the `funcionDelegateCall()` call. If this were to happen, the inner `multicall()` concluding would set `isMulticall` to `false` before the outer `multicall()` has concluded. If there are more calls after the outer `multicall()` invoked the inner `multicall()`, the `isMulticall` variable will incorrectly be `false`. Fortunately this doesn't seem to have major consequences.

