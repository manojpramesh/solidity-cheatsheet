# Solidity Cheatsheet and Best practices

## Motivation

This document is a cheatsheet for **Solidity** that you can use to write **Smart Contracts** for **Ethereum** based blockchain.

This guide is not intended to teach you Solidity from the ground up, but to help developers with basic knowledge who may struggle to get familiar with Smart Contracts and Blockchain because of the Solidity concepts used.

> Older versions of this cheatsheet can be found in Git tags.


## Table of contents

- [Solidity Cheatsheet and Best practices](#solidity-cheatsheet-and-best-practices)
  * [Motivation](#motivation)
  * [Table of contents](#table-of-contents)
  * [Version pragma](#version-pragma)
  * [Import files](#import-files)
  * [Types](#types)
    + [Boolean](#boolean)
    + [Integer](#integer)
      - [Unchecked arithmetic](#unchecked-arithmetic)
    + [Address](#address)
      - [balance](#balance)
      - [transfer and send](#transfer-and-send)
      - [call](#call)
      - [delegatecall](#delegatecall)
      - [callcode](#callcode)
    + [Array](#array)
    + [Fixed byte arrays](#fixed-byte-arrays)
    + [Dynamic byte arrays](#dynamic-byte-arrays)
    + [Enum](#enum)
    + [Struct](#struct)
    + [Mapping](#mapping)
    + [Data locations](#data-locations)
  * [Control Structures](#control-structures)
  * [Functions](#functions)
    + [Structure](#structure)
    + [Access modifiers](#access-modifiers)
    + [Parameters](#parameters)
      - [Input parameters](#input-parameters)
      - [Output parameters](#output-parameters)
    + [Constructor](#constructor)
    + [Function Calls](#function-calls)
      - [Internal Function Calls](#internal-function-calls)
      - [External Function Calls](#external-function-calls)
      - [Named Calls](#named-calls)
      - [Unnamed function parameters](#unnamed-function-parameters)
    + [Function type](#function-type)
    + [Function Modifier](#function-modifier)
    + [View or Constant Functions](#view-or-constant-functions)
    + [Pure Functions](#pure-functions)
    + [Payable Functions](#payable-functions)
    + [Fallback Function](#fallback-function)
      - [Receive vs Fallback](#receive-vs-fallback)
  * [Contracts](#contracts)
    + [Creating contracts using `new`](#creating-contracts-using-new)
    + [Contract Inheritance](#contract-inheritance)
      - [Multiple inheritance](#multiple-inheritance)
      - [Constructor of base class](#constructor-of-base-class)
    + [Abstract Contracts](#abstract-contracts)
  * [Interface](#interface)
  * [Events](#events)
  * [Library](#library)
  * [Using - For](#using---for)
  * [Units](#units)
  * [Error Handling](#error-handling)
    + [Custom Errors](#custom-errors)
    + [Try / Catch](#try--catch)
  * [Global variables](#global-variables)
    + [Block variables](#block-variables)
    + [Transaction variables](#transaction-variables)
    + [Mathematical and Cryptographic Functions](#mathematical-and-cryptographic-functions)
    + [Contract Related](#contract-related)


## Version pragma

`pragma solidity ^0.8.34;` will compile with a compiler version `>= 0.8.34` and `< 0.9.0`.


## Import files

`import "filename";`

`import * as symbolName from "filename";` or `import "filename" as symbolName;`

`import {symbol1 as alias, symbol2} from "filename";`


## Types

### Boolean

`bool` : `true` or `false`

Operators:

- Logical : `!` (logical negation), `&&` (AND), `||` (OR)
- Comparisons : `==` (equality), `!=` (inequality)

### Integer

Unsigned : `uint8 | uint16 | uint32 | uint64 | uint128 | uint256(uint)`

Signed   : `int8 | int16 | int32 | int64 | int128 | int256(int)`

Operators:

- Comparisons: `<=`, `<`, `==`, `!=`, `>=` and `>`
- Bit operators: `&`, `|`, `^` (bitwise exclusive or) and `~` (bitwise negation)
- Arithmetic operators: `+`, `-`, unary `-`, unary `+`, `*`, `/`, `%`, `**` (exponentiation), `<<` (left shift) and `>>` (right shift)

> Arithmetic overflow and underflow checks are enabled by default in Solidity `0.8.x`. Use `unchecked { ... }` only when you intentionally want wrapping behavior.

#### Unchecked arithmetic

Use `unchecked` to disable the default overflow and underflow checks inside a specific block.

```solidity
function add(uint256 a, uint256 b) public pure returns (uint256) {
    unchecked {
        return a + b;
    }
}
```

### Address

`address`: Holds an Ethereum address (20 byte value).

`address payable`: Same as address, but is used for addresses that can receive `Ether`.

Operators:

- Comparisons: `<=`, `<`, `==`, `!=`, `>=` and `>`

Methods:

#### balance

- `<address>.balance (uint256)`: balance of the Address in Wei

#### transfer and send

- `<address payable>.transfer(uint256 amount)`: send given amount of Wei to Address, throws on failure
- `<address payable>.send(uint256 amount) returns (bool)`: send given amount of Wei to Address, returns false on failure

> In the current version of Solidity, `transfer` and `send` are considered legacy patterns. Prefer `call{value: amount}("")` and always check the returned success value.

#### call

- `<address>.call(bytes memory data) returns (bool, bytes memory)`: issue low-level `CALL`

```solidity
(bool success, bytes memory result) = target.call{value: amount}(data);
```

#### delegatecall

- `<address>.delegatecall(bytes memory data) returns (bool, bytes memory)`: issue low-level `DELEGATECALL`

Delegatecall uses the code of the target address, taking all other aspects (storage, balance, ...) from the calling contract. The purpose of delegatecall is to use library or proxy code which is stored in another contract. The user has to ensure that the layout of storage in both contracts is suitable for delegatecall to be used.

```solidity
contract A {
    uint256 value;
    address public sender;
    address target;

    function makeDelegateCall(uint256 _value) public {
        (bool success, ) = target.delegatecall(
            abi.encodeWithSignature("setValue(uint256)", _value)
        );
        require(success, "delegatecall failed");
    }
}

contract B {
    uint256 value;
    address public sender;

    function setValue(uint256 _value) public {
        value = _value;
        sender = msg.sender;
    }
}
```

> Use call options such as `{gas: gasAmount}` and `{value: amount}` instead of the older `.gas()` and `.value()` syntax.

#### callcode

`callcode` is obsolete and should not be used in modern Solidity.

### Array

Arrays can be dynamic or have a fixed size.

```solidity
uint256[] dynamicSizeArray;

uint256[7] fixedSizeArray;
```

### Fixed byte arrays

`bytes1`, `bytes2`, `bytes3`, ..., `bytes32`.

Operators:

Comparisons: `<=`, `<`, `==`, `!=`, `>=`, `>` (evaluate to bool)

Bit operators: `&`, `|`, `^` (bitwise exclusive or), `~` (bitwise negation), `<<` (left shift), `>>` (right shift)

Index access: If `x` is of type `bytesI`, then `x[k]` for `0 <= k < I` returns the `k` th byte (read-only).

Members

- `.length` : read-only

### Dynamic byte arrays

`bytes`: Dynamically-sized byte array.

`string`: Dynamically-sized UTF-8 encoded string. It is similar to `bytes`, but does not allow length or index access directly.

### Enum

Enum works just like in every other language.

```solidity
enum ActionChoices {
    GoLeft,
    GoRight,
    GoStraight,
    SitStill
}

ActionChoices choice = ActionChoices.GoStraight;
```

### Struct

New types can be declared using struct.

```solidity
struct Funder {
    address addr;
    uint256 amount;
}

Funder funder;
```

### Mapping

Declared as `mapping(_KeyType => _ValueType)`

Mappings can be seen as **hash tables** which are virtually initialized such that every possible key exists and is mapped to a value.

**key** can be any built-in value type, `bytes`, `string`, contract type, or enum. **value** can be almost any type, including mappings, arrays and structs.

### Data locations

Reference types such as arrays, structs, `bytes` and `string` use data locations.

- `storage` - persistent state stored on-chain
- `memory` - temporary data for the current call
- `calldata` - read-only input data for external calls

```solidity
string public name;

function setName(string calldata _name) external {
    name = _name;
}
```

> Mappings can only exist in `storage`.


## Control Structures

Most of the control structures from JavaScript are available in Solidity except for `switch` and `goto`.

- `if` `else`
- `while`
- `do while`
- `for`
- `break`
- `continue`
- `return`
- `? :`
- `try` `catch`
- `unchecked`

## Functions

### Structure

`function <functionName>(<parameter types>) {internal|external|public|private} [pure|view|payable] [virtual|override] [returns (<return types>)]`

### Access modifiers

- `public` - Accessible from this contract, inherited contracts and externally
- `private` - Accessible only from this contract
- `internal` - Accessible only from this contract and contracts inheriting from it
- `external` - Cannot be accessed directly internally, only externally. Access internally with `this.f(...)` or by using an internal helper function

### Parameters

#### Input parameters

Parameters are declared just like variables. Reference types should use an explicit data location such as `memory` or `calldata`.

```solidity
function f(uint256 _a, uint256 _b) public pure {}
```

#### Output parameters

Output parameters are declared after the `returns` keyword

```solidity
function f(uint256 _a, uint256 _b) public pure returns (uint256 _sum) {
    _sum = _a + _b;
}
```

Output can also be specified using `return` statement. In that case, we can omit parameter name `returns (uint256)`.

Multiple return types are possible with `return (v0, v1, ..., vn)`.


### Constructor

Function that is executed during contract deployment. Defined using the `constructor` keyword.

```solidity
contract C {
    address owner;
    uint256 status;

    constructor(uint256 _status) {
        owner = msg.sender;
        status = _status;
    }
}
```

### Function Calls

#### Internal Function Calls

Functions of the current contract can be called directly (internally - via jumps) and also recursively

```solidity
contract C {
    function funA() internal pure returns (uint256) {
        return 5;
    }

    function funB(uint256 _a) public pure returns (uint256 ret) {
        return funA() + _a;
    }
}
```

#### External Function Calls

`this.g(8);` and `c.g(2);` (where `c` is a contract instance) are also valid function calls, but the function will be called externally, via a message call.

> Call options such as `{gas: gasAmount, value: amount}` can also be used with external function calls.

#### Named Calls

Function call arguments can also be given by name in any order as below.

```solidity
function f(uint256 a, uint256 b) public pure {}

function g() public pure {
    f({b: 1, a: 2});
}
```

#### Unnamed function parameters

Parameters will be present on the stack, but are not accessible.

```solidity
function f(uint256 a, uint256) public pure returns (uint256) {
    return a;
}
```

### Function type

Pass function as a parameter to another function. Similar to `callbacks` and `delegates`

```solidity
contract Oracle {
    struct Request {
        bytes data;
        function(bytes memory) external callback;
    }

    Request[] requests;
    event NewRequest(uint256);

    function query(bytes memory data, function(bytes memory) external callback) external {
        requests.push(Request(data, callback));
        emit NewRequest(requests.length - 1);
    }

    function reply(uint256 requestID, bytes memory response) external {
        requests[requestID].callback(response);
    }
}

contract OracleUser {
    Oracle public oracle;

    constructor(address _oracle) {
        oracle = Oracle(_oracle);
    }

    function buySomething() external {
        oracle.query(bytes("USD"), this.oracleResponse);
    }

    function oracleResponse(bytes memory) external {
        require(msg.sender == address(oracle), "only oracle");
    }
}
```


### Function Modifier

Modifiers can automatically check a condition prior to executing the function.

```solidity
address owner;
uint256 status;

modifier onlyOwner() {
    require(msg.sender == owner, "not owner");
    _;
}

function setStatus(uint256 _status) public onlyOwner {
    status = _status;
}
```

### View or Constant Functions

Functions can be declared `view` in which case they promise not to modify the state, but can read from it. Older examples may use `constant`, but `view` is the current syntax.

```solidity
uint256 b;

function f(uint256 a) public view returns (uint256) {
    return a * b;
}
```

### Pure Functions

Functions can be declared `pure` in which case they promise not to read from or modify the state.

```solidity
function f(uint256 a) public pure returns (uint256) {
    return a * 42;
}
```

### Payable Functions

Functions that receive `Ether` are marked as `payable` function.

### Fallback Function

A contract can have a `receive()` function and a `fallback()` function. These functions cannot return anything. `receive()` is executed on plain Ether transfers with empty calldata. `fallback()` is executed if none of the other functions match the given function identifier.

#### Receive vs Fallback

- `receive()` is called when the calldata is empty and Ether is sent
- `fallback()` is called when no other function matches
- `fallback()` can also be marked `payable` if it should receive Ether

```solidity
receive() external payable {
    // Receive Ether
}

fallback() external payable {
    // Do something
}
```

## Contracts

### Creating contracts using `new`

Contracts can be created from another contract using `new` keyword. The source of the contract has to be known in advance.

```solidity
contract A {
    function add(uint256 _a, uint256 _b) public pure returns (uint256) {
        return _a + _b;
    }
}

contract C {
    A a;

    function f() public {
        a = new A();
    }
}
```

### Contract Inheritance

Solidity supports multiple inheritance and polymorphism.

```solidity
contract Owned {
    address owner;

    constructor() {
        owner = msg.sender;
    }
}

contract Mortal is Owned {
    function close() public virtual {
        require(msg.sender == owner, "not owner");
    }
}

contract Final is Mortal {
    function close() public override {
        super.close();
    }
}
```

#### Multiple inheritance

```solidity
contract A {}
contract B {}
contract C is A, B {}
```

#### Constructor of base class

```solidity
contract A {
    uint256 a;

    constructor(uint256 _a) {
        a = _a;
    }
}

contract B is A {
    constructor(uint256 _b) A(_b) {}
}
```


### Abstract Contracts

Contracts that contain implemented and non-implemented functions. Such contracts cannot be deployed, but they can be used as base contracts.

```solidity
abstract contract A {
    function c() public view virtual returns (bytes32);
}

contract B is A {
    function c() public pure override returns (bytes32) {
        return "c";
    }
}
```

## Interface

`Interfaces` are similar to abstract contracts, but they have restrictions:

- Cannot have any functions implemented.
- Cannot inherit other contracts, but can inherit other interfaces.
- Cannot define constructor.
- Cannot define state variables.
- Can define structs, enums, errors and events.

```solidity
interface Token {
    function transfer(address recipient, uint256 amount) external returns (bool);
}
```

## Events

Events allow the convenient usage of the EVM logging facilities, which in turn can be used to "call" JavaScript callbacks in the user interface of a dapp, which listen for these events.

Up to three parameters can receive the attribute `indexed`, which will cause the respective arguments to be searchable.

> All non-indexed arguments will be stored in the data part of the log.

```solidity
contract ClientReceipt {
    event Deposit(
        address indexed _from,
        bytes32 indexed _id,
        uint256 _value
    );

    function deposit(bytes32 _id) external payable {
        emit Deposit(msg.sender, _id, msg.value);
    }
}
```

## Library

Libraries are similar to contracts, but they are deployed only once at a specific address, and their code can be reused by other contracts. Public library functions use `delegatecall`; internal ones are included in the caller.

```solidity
library Arithmetic {
    function add(uint256 _a, uint256 _b) internal pure returns (uint256) {
        return _a + _b;
    }
}

contract C {
    uint256 sum;

    function f() public {
        sum = Arithmetic.add(2, 3);
    }
}
```

## Using - For

`using A for B;` can be used to attach library functions to any type.

```solidity
library Arithmetic {
    function add(uint256 _a, uint256 _b) internal pure returns (uint256) {
        return _a + _b;
    }
}

contract C {
    using Arithmetic for uint256;

    uint256 sum;

    function f(uint256 _a) public {
        sum = _a.add(3);
    }
}
```

## Units

Ether units:

- `wei`
- `gwei`
- `ether`

Time units:

- `seconds`
- `minutes`
- `hours`
- `days`
- `weeks`

```solidity
uint256 public oneEther = 1 ether;
uint256 public oneMinute = 1 minutes;
```

> Time units are simple suffixes for readability. Older examples may include `years`, but that is not supported in the current version of Solidity.

## Error Handling

- `assert(bool condition)`: throws if the condition is not met - to be used for internal errors.
- `require(bool condition)`: throws if the condition is not met - to be used for errors in inputs or external components.
- `revert()`: abort execution and revert state changes
- Custom errors can also be used with `revert MyError(...)`

### Custom Errors

Custom errors are a gas-efficient way to return structured failure information.

```solidity
address owner;

error Unauthorized(address caller);

function adminOnly() public view {
    if (msg.sender != owner) revert Unauthorized(msg.sender);
}
```

```solidity
error SendFailed();

function sendHalf(address payable addr) public payable returns (uint256 balance) {
    require(msg.value % 2 == 0, "Only allow even numbers");
    uint256 balanceBeforeTransfer = address(this).balance;

    (bool success, ) = addr.call{value: msg.value / 2}("");
    if (!success) revert SendFailed();

    assert(address(this).balance == balanceBeforeTransfer - msg.value / 2);
    return address(this).balance;
}
```

> Catching exceptions is possible using `try/catch` for external calls and contract creation.

### Try / Catch

`try/catch` can be used with external function calls and contract creation.

```solidity
try otherContract.doWork() returns (uint256 value) {
    return value;
} catch {
    revert("external call failed");
}
```

## Global variables

### Block variables

- `blockhash(uint256 blockNumber) returns (bytes32)`: hash of the given block - only works for the 256 most recent blocks excluding current
- `block.coinbase (address)`: current block validator or miner address
- `block.gaslimit (uint256)`: current block gaslimit
- `block.number (uint256)`: current block number
- `block.timestamp (uint256)`: current block timestamp as seconds since unix epoch
- `block.chainid (uint256)`: current chain id
- `block.basefee (uint256)`: current block base fee
- `block.prevrandao (uint256)`: randomness value provided by the beacon chain

### Transaction variables

- `msg.data (bytes)`: complete calldata
- `gasleft() returns (uint256)`: remaining gas
- `msg.sender (address)`: sender of the message (current call)
- `msg.sig (bytes4)`: first four bytes of the calldata (i.e. function identifier)
- `msg.value (uint256)`: number of wei sent with the message
- `tx.gasprice (uint256)`: gas price of the transaction
- `tx.origin (address)`: sender of the transaction (full call chain)

### Mathematical and Cryptographic Functions

- `addmod(uint256 x, uint256 y, uint256 k) returns (uint256)`:
   compute `(x + y) % k` where the addition is performed with arbitrary precision and does not wrap around at `2**256`.
- `mulmod(uint256 x, uint256 y, uint256 k) returns (uint256)`:
   compute `(x * y) % k` where the multiplication is performed with arbitrary precision and does not wrap around at `2**256`.
- `keccak256(...) returns (bytes32)`:
   compute the Ethereum-SHA-3 (Keccak-256) hash of the arguments
- `sha256(...) returns (bytes32)`:
   compute the SHA-256 hash of the arguments
- `ripemd160(...) returns (bytes20)`:
   compute RIPEMD-160 hash of the arguments
- `ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)`:
   recover the address associated with the public key from elliptic curve signature or return zero on error

> Older examples may use `sha3(...)`. In recent versions of Solidity use `keccak256(...)`.

### Contract Related

- `this (current contract's type)`: the current contract, explicitly convertible to Address
- `address(this).balance (uint256)`: balance of the current contract in Wei
- `selfdestruct(address payable recipient)`: destroy the current contract, sending its funds to the given Address. Use with caution.

> Older examples may use `now` and `suicide`. In recent versions of Solidity use `block.timestamp` and `selfdestruct`.
