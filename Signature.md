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