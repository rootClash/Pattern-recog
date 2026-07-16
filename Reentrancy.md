## PoC: 
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
## PoC: 
viloation of CEI pattern

### Pattern
cross function Reentrancy

### Root Cause
viloation of CEI pattern lead to drainage of protocol

### Assumption
after the lockTime Pass on their current lock,  the user can split the lock   
overpayment trigger refund

### Broken Invariant
Sum of all lock amounts in the Locker vault == Total ERC20 tokens held by the Locker contract

### Attack Story
attacker owned the asset in Locker as Token => attacker call split the lock when he havent withdrawn the last locker => attacker overpaid the eth => eth was transfered to user before state update => lead to reentrancy => attacker called withdraw and pull out the all asset => and then state varaible change with the current asset new lock was created.

### Checklist
follow cei pattern that is important

### Mitigation
use Reentrancy guard.

``--------------------------------------------------------------------------------``
## PoC: Voliation of CEI 

### Pattern
logic 

### Root Cause
violation of CEI pattern

### Assumption
-> no working capital needed 
-> amount will be send to the trusted user 
-> The price of the asset in market assumed to be same

### Broken Invariant
=> the price of the asset in market is same but The attacker didn't break an invariant (like a mathematical property that should always hold). Instead, they exploited a logic bug in the contract's execution flow.

### Attack Story
user called the tesseraSwapWithCallback() function => added the amountOut and amountIn => inside the user callback function => it buys the input asset from the another exchange at low price then this Tessera Engine => pay back the input asset => and gain some profit. the attacker loop this more than 100 times.

### Checklist
-> undestand the flow how the flow should work.
-> sometimes without invariant break the protocol can be drained.

### Mitigation
-> use CEI pattern.

