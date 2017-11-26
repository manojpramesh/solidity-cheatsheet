### WIP Version. Feel free to contribute

# Solidity cheatsheet
Cheat sheet for solidity

## Version

`pragma solidity ^0.4.18;`  will compile with a compiler version  > 0.4.0 and < 0.5.0.

## Import files

`import "filename";`

`import * as symbolName from "filename";` or `import "filename" as symbolName;`

`import {symbol1 as alias, symbol2} from "filename";`


## Types

### Boolean

`bool` : `true` or `false`

Operators:

- ! (logical negation)
- && (AND)
- || (OR)
- == (equality)
- != (inequality)

### Integer

Unsigned : `uint8 | uint16 | uint32 | uint64 | uint128 | uint256(uint)`

Signed   : `int8  | int16  | int32  | int64  | int128  | int256(uint) `

Operators:

- Comparisons: <=, <, ==, !=, >= and >
- Bit operators: &, |, ^ (bitwise exclusive or) and ~ (bitwise negation)
- Arithmetic operators: +, -, unary -, unary +, *, /, %, ** (exponentiation), << (left shift) and >> (right shift)

### Address

`address`: Holds an Ethereum address (20 byte value).

Operators:

- Comparisons: <=, <, ==, !=, >= and >

Methods:

- `<address>.balance (uint256)`: balance of the Address in Wei
- `<address>.transfer(uint256 amount)`: send given amount of Wei to Address, throws on failure
- `<address>.send(uint256 amount) returns (bool)`: send given amount of Wei to Address, returns false on failure
- `<address>.call(...) returns (bool)`: issue low-level CALL, returns false on failure
- `<address>.callcode(...) returns (bool)`: issue low-level CALLCODE, returns false on failure
- `<address>.delegatecall(...) returns (bool)`: issue low-level DELEGATECALL, returns false on failure


### Fixed byte arrays

`bytes1(byte)`, `bytes2`, `bytes3`, ..., `bytes32`.

Operators:

Comparisons: `<=`, `<`, `==`, `!=`, `>=`, `>` (evaluate to bool)
Bit operators: `&`, `|`, `^` (bitwise exclusive or), `~` (bitwise negation), `<<` (left shift), `>>` (right shift)
Index access: If x is of type bytesI, then x[k] for 0 <= k < I returns the k th byte (read-only).

Members

- `.length` : read-only

### Dynamic byte arrays

`bytes`: Dynamically-sized `byte` array. It is similar to `byte[]`, but it is packed tightly in calldata. Not a value-type!
`string`: Dynamically-sized UTF-8-encoded string. It is equal to `bytes` but does not allow length or index access. Not a value-type!

### Enum

Declaration:

`enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }`

Usage:

`ActionChoices choice = ActionChoices.GoStraight;`

### Struct

### Mapping

### Array


## Functions

### Structure

```function (<parameter types>) {internal|external|public|private} [pure|constant|view|payable] [returns (<return types>)]```

### Access modifiers

- ```public``` - Accessible from this contract, inherited contracts and externally
- ```private``` - Accessible only from this contract
- ```internal``` - Accessible only from this contract and contracts inheriting from it
- ```external``` - Cannot be accessed internally, only externally. Recommended to reduce gas. Access internally with `this.f`.

### Function type

Pass function as a parameter to another function. Similar to `callbacks` and `delegates`

```
pragma solidity ^0.4.18;

contract Oracle {
  struct Request {
    bytes data;
    function(bytes memory) external callback;
  }
  Request[] requests;
  event NewRequest(uint);
  function query(bytes data, function(bytes memory) external callback) {
    requests.push(Request(data, callback));
    NewRequest(requests.length - 1);
  }
  function reply(uint requestID, bytes response) {
    // Here goes the check that the reply comes from a trusted source
    requests[requestID].callback(response);
  }
}

contract OracleUser {
  Oracle constant oracle = Oracle(0x1234567); // known contract
  function buySomething() {
    oracle.query("USD", this.oracleResponse);
  }
  function oracleResponse(bytes response) {
    require(msg.sender == address(oracle));
  }
}
```


### Function Modifier

### Pure | View | Constant | Payable


## Library

## Interface


### TODO

- events
- enums
- functions
- library
- modifiers
- struct
- mapping
