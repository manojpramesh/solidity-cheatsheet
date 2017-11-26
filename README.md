## WIP Version. Feel free to contribute

# Solidity cheatsheet
Cheat sheet for solidity

### Version

```pragma solidity ^0.4.18;```  will compile with a compiler version  > 0.4.0 and < 0.5.0.

### Import files

```import "filename";```

```import * as symbolName from "filename";``` or ```import "filename" as symbolName;```

```import {symbol1 as alias, symbol2} from "filename";```


### Types

#### Boolean

`bool` : `true` or `false`

Operators:

- ! (logical negation)
- && (AND)
- || (OR)
- == (equality)
- != (inequality)

#### Integer

Unsigned : `uint8 | uint16 | uint32 | uint64 | uint128 | uint256(uint)`

Signed   : `int8  | int16  | int32  | int64  | int128  | int256(uint) `

Operators:

- Comparisons: <=, <, ==, !=, >= and >
- Bit operators: &, |, ^ (bitwise exclusive or) and ~ (bitwise negation)
- Arithmetic operators: +, -, unary -, unary +, *, /, %, ** (exponentiation), << (left shift) and >> (right shift)

#### Address

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


#### 

### Functions

#### Structure

```function (<parameter types>) {internal|external|public|private} [pure|constant|view|payable] [returns (<return types>)]```

#### Access modifiers

- ```public``` - Accessible from this contract, inherited contracts and externally
- ```private``` - Accessible only from this contract
- ```internal``` - Accessible only from this contract and contracts inheriting from it
- ```external``` - Cannot be accessed internally, only externally. Recommended to reduce gas.




#### TODO

- events
- enums
- functions
- library
- modifiers
- struct
- mapping
