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

Avoid using external calls whenever possible. Calls to untrusted code can lead to security flaw.

Use External calls at the bottom of the function as it will forward all the gas to the funtion.

### Error handling

Comming soon
