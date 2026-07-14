## PoC: Missing Nonce
missing global mutex

### Pattern
classic Reentrancy Through the ERC777

### Root Cause
due to not updating the token balance after transfer to the user.

### Assumption
the input amount < then the balance of the bankManager

### Broken Invariant
token balance transferred and backedAsset value should change automatically

### Attack Story
attacker set the asset [someERC777 , token (USDC)] => balanceManage (200 someERC777 and 100 tokens) => attacker ke pass (30 RSTOKEN) => user gets the approval from the battery contract => attacker reddem, 15k someERC777 15 usdc => balance(balanceManager : 185k ERC777 , 100 tokens) and backsetNeeded 135 token => now attacker hook runs and call redeem again => transfer remaining 15 some ERC777 with 10 usdc token after calculation => till then the basketNeeded 120 RSTK token => and balanceManager balance 170 token , 89 token => and remainig 11.1 usdc angain send to the attacker => total 30 ERC777 and 21.1  usdc in total.

### Checklist
any external call like .transfer() transferFrom() , call() should not be trusted because it can be change in future.

### Mitigation
whenever you see ERC20 .transfer() then always check whether the token being used is ERC777 .because the ERC777 is backward compatiable with ERC20 as the result the Hooks can lead to the classic Reentracy guard.

add the centeral Reenterancy guard and also check the state changes during the call (that is importnat)

``------------------------------------------------------------------------------``
