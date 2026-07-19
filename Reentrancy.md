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

``---------------------------------------------------------------------------``
## PoC: CEI VIOLATION

### Pattern
read only reentracy

### Root Cause
violation of CEI pattern

### Assumption
-> user has lpTokens
-> lpTokens approved for burn
-> tokens are received proportionally

### Broken Invariant
=> reserve value does not update after the burn and transfer 

### Attack Story
attacker call the removeLiquidity => burn its lpmtoken on the basic of current price =>
after transfer => as the preserve price is not updated => attacker buy at the same stale price => or check which protocol use the Beanstalk walls for reserve price => and buy at less price.
### Checklist
=> follow the reentrancy guard  
=> never belive on view function 

### Mitigation
=> follow the reentrancy guard
=> keep the eye on view function

```---------------------------------------------------------------------------------```
## PoC: 
total supply token does not update in `BalancerVault`

### Pattern
read only Reentrancy

### Root Cause
`BalancerPairOracle.getPrice` does not update the total token reserve

### Assumption
The BalanceVault contract provide the current updated reserve 

### Broken Invariant
LP Price × LP Total Supply = Total Economic Value of the Pool

### Attack Story
attaker use the Flash loan => used the asset  to join => which lead to renter the contract => then call lquidate => which envoked getPrice function => but the previous total token was not updated => whereas the total supply of LP token did => in the end the attacker used the last token price => and drained the protocol.

### Checklist
never beleive on getter function which act as calculator. 

### Mitigation
-> use reentrancy guard in getter function.
-> use verfied oracle.


``----------------------------------------------------------------------------``
## PoC: Missing Reentrancy guard lead to Cross contract Reentrancy and DOS attack

### Pattern
Cross contract Reentrancy + DOS attack from user side

### Root Cause
Violation of CEI pattern + no Proper accounting

### Assumption
proper accouting of eth deposit from both the Node runner and  User side.

### Broken Invariant
sum of all ETH deposited by the node runner Through registerd BLB key == eth Deposited to the vault Through Registered BLB key.

### Attack Story
attacker register the BLB key => add the 4eth token as a stake => call the `withdrawEthForKnot()` => this function violates the CEI pattern => as the result the attacker reEnter through this => 4 eth back to the attacker => then through receive => reenter stake brcause of again viloation of CEI patteren => and vault return the user deposited amount to attacker => then attacker address get BLOCKED => now when user call mintLpToken or burn or withdraw => it revert sign of DOS attack

### Checklist
- do the proper accounting are done?
- does the function using external call follow CEI pattern?
- does it have Reentracy guard?

### Mitigation
- use CEI pattern
- proper accounting of asset

``------------------------------------------------------------------------------------------------------``
## PoC: Missing cross contract Reentranct (Medium)

### Pattern
Cross contract Reentrancy with (ERC721)

### Root Cause
Missing Reentrancy check

### Assumption
No user have buyed the NFT

### Broken Invariant
One NFT for single userid

### Attack Story
inside the `mintPublic` function => `bytes feeRecepient` parameter is there => attacker firt time use ETH fee recepiet +> got one NFT => use another though the ERC721 hook => using USDC fee Receipt => got another.

### Checklist
- follow CEI pattern?

### Mitigation
- use Reentrancy guard and CEI pattern
- aware about the Type of hooks Token