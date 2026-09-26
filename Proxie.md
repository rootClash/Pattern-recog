## PoC: An attacker is able to hijack any ERC721 / ERC1155 he borrows because guard is missing validation on the address supplied to function call `setFallbackHandler()` reNFT

### Pattern
access control + no validation in address check + used fallback in exploitable 

### Root Cause
The `setFallbackHandler()` does not have any access control hence can be called by anyone

### Assumption
The user is providing, the correct address in the `setFallbackHandler()`. 

### Broken Invariant
The fallaback address must always be trusted/valid handler for the Rental safe.

### Attack Story
1. Attacker rent the reNFt (Owner is : ReNFT contract)
2. attacker use `setFallbackHandler()` and set the address to the nft contract
3. attacker use the fallback and call the transferFrom
4. hence the `msg.sender` is reNFT contract. 
5. Through fallback the handler call the , `transferFrom()` in NFT contract.
6. as the result the nft get transfered to the attacker 

### Checklist
[] always check  the access control inside the setter function
[] validate the address inside inside the function

### Mitigation
* use access control inside the setter function
* valdiate the address to used inisde the fallaback function.

## PoC: Initialization :
Storage collision between the Proxie and Implementation contract

### Pattern
Storage collision

### Root Cause
Storage collsion due to not using EIP1967

### Assumption
Storage collison will not occur

### Broken Invariant
Stoage collision collided

### Attack Story
no POC or attack mentioned

### Checklist
[] check the storage in implementation and the Proxy contract with type

### Mitigation
To use EIP1967

## PoC:  Holographable tokens can be reinitialized Holograph 

### Pattern
re initialization can be done

### Root Cause
The reentracy inside the initialization().

### Assumption
initialzation() can be called once.

### Broken Invariant
The initializon() can be called through reentrancy.

### Attack Story
1. attacker call iniatialize function
2. use reentrancy in that function
3. as the result reenter in that function and make changes
4. again continue the same function

### Checklist
check whether initialize() uses OZ initializer library or not?

### Mitigation
use OZ initializer library function.