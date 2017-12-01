### WIP Version. Pending review. Use with caution

# Solidity security guidelines & Best practices

Solidity cheatsheet is available in [solidity-cheatsheet](/README.md).

## Tips & Tricks
- Ethereum will be updated very frequently until a stable build. So expect new best practices and security consideration every now and then.
- Your code will have bugs and be prepared to handle those. Use techniques such as pausing the contract and limiting usage.
- Keep your contract simple and Modularize.
- Make use of libraries so that you can reuse and update code.
- Try to use already available tools and libraries and update them frquently.
- Never try to store and implement everything in solidity. Use it where decentralization is needed.


### External calls

Avoid using external calls whenever possible. Calls to untrusted code can lead to security flaw. When using external contract calls, assume that unsafe code might execute. Even if the contract is not malicious, malicious code can be executed by any contracts it calls.

Use External calls at the bottom of the function as it will forward all the gas to the funtion.

Use `<address>.call.gas(gasAmount)()` to limit sending gas to external calls.


### Sending transaction from contracts

`<address>.send()` and `<address>.transfer()` are considered safe. It has a limited gas of `2300` which is low for any task except for an event.

`<address>.transfer()` will revert the transaction and `<address>.send()` will return false. Use it based on requirement.

### Error handling

Some functions will return false if it fails. Make sure to handle the possibility that the call will fail, by checking the return value.

`call`, `callcode`, `delegatecall`, `send` are some functions that return `false` on failure.

```
// BAD
<address>.send(10);

// GOOD

// Including success and failure callbaks
if(<address>.send()) { 
    // OnSuccess
} else {
    // OnFail
}

// Using assert that will revert the transaction on false
assert(<address>.send())
```

### Using access modifiers

Use access modifiers explicitly. Using `external` for external only function will reduce gas.

```
// BAD
function externalFunction() {
    // Do Something
}

// GOOD
uint public status;
function externalFunction() external {
    // Do Something
}
```

### Make use of function modifiers

Use modifiers to restrict access to functions. Avoid unauthorized access for all functions. You should use `msg.sender` over `tx.origin` for authorization.

```
// BAD
function selfdestruct() {
    selfdestruct(<address>);
}

// GOOD
modifier onlyOwner() {
  require(msg.sender == <address of owner>);
  _
}
function selfdestruct() onlyOwner {
    selfdestruct(<address>);
}
```

### Upgrading contracts

Use `delegatecall` in a proxy contract for upgrading contracts overtime.
