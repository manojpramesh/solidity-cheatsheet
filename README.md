## WIP Version. Feel free to contribute

# Solidity cheatsheet
Cheat sheet for solidity

### Version

```pragma solidity ^0.4.18;```  will compile with a compiler version  > 0.4.0 and < 0.5.0.

### Import files

```import "filename";```

```import * as symbolName from "filename";``` or ```import "filename" as symbolName;```

```import {symbol1 as alias, symbol2} from "filename";```

### Functions

#### Access modifiers

- ```public``` - Accessible from this contract, inherited contracts and externally
- ```private``` - Accessible only from this contract
- ```internal``` - Accessible only from this contract and contracts inheriting from it
- ```external``` - Cannot be accessed internally, only externally. Recommended to reduce gas.
