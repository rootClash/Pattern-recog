## PoC: bad desgin of implementing the mapping for bytes32 data

### Pattern
Signature Mallebiality attack

### Root Cause
using mappintg(bytes32 => bool ) . as this was used, the hash value of a signature are malleable. for same v,r,s the hash value can be same

### Assumption
hash value are different

### Broken Invariant
hash value are same

### Attack Story
NO SUCH ATTACK STORY : but attacker can generate the hash of specific signature with same v,r,s value.which will make it indetical inside the mapping.

### Checklist
check whether hash data is sotred in mapping with bool value eg: mapping(bytes32 => bool)?
-> does it is using nonce , chain id , address(this) ,checking the length of s and v ?
-> using openzepplin library or ECDSA library?

### Mitigation
-> use openzepplin library
-> use desgin pattern like  mapping(bytes32 => uint256 ) -> which will be used a nonce ( a unique identifier)
-> check whether other data like chain id , addresst(this)
-> Using EIP721 

## PoC: Arbitrary transactions possible due to insufficient signature validation

### Pattern
No owenr address check inside the `checkSignatures` function

### Root Cause
The `checkSignatures` function does not validate that  `_signer` address is the owner address 

### Assumption
The `signature` passed is the Owner signed signature

### Broken Invariant
_signer address recovered == owner address

### Attack Story
The attacker can pass the condition inside the function `require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");`once it create a malleable signature . 
* can desturct the contract
* can add new function through which new implementation can be added
* can break the proxy
* can steal the asset 

### Checklist
When you encounter EIP-1271:

[ ] Who is the real owner?
[ ] Where does signer come from?
[ ] Can attacker control signer?
[ ] Is signer == owner checked?
[ ] Is isValidSignature() or similar function called on attacker-controlled address?
[ ] Does the signature bind the complete transaction?
[ ] Is nonce protected?
[ ] Is replay prevented?
[ ] What can be executed if validation passes?
[ ] Can CALL/DELEGATECALL/upgrade be abused?

### Mitigation
[ ] Use OpenZeppelin ERC20Permit where possible
[ ] Use EIP-712 domain separation
[ ] Include verifyingContract
[ ] Include chainId
[ ] Include nonce
[ ] Include deadline
[ ] Include owner
[ ] Include spender
[ ] Include value
[ ] Recover signer
[ ] Require recovered signer == owner
[ ] Increment nonce after successful permit
[ ] Reject expired deadlines