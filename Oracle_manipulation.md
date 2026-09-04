## PoC: Price Manipulation risk in GameSwap vault

### Pattern
Spot price manipulation

### Root Cause
using the single uniswap v3 pool for spot price

### Assumption
The collateral value of LP provider should not loose the economic value of the pool of the deposited assets.

### Broken Invariant
Trusting the Pool for the fair market 

### Attack Story
attacker will borrow loan => and use the small window as a attacking approch.

### Checklist
whenver you find any function related like
- _getCurrentPrice()
- _getSpotPrice()

which calcualted the spot price from the single pool please investigate that

### Mitigation
-> use TWAp
-> use external oracle like chainlink

``----------------------------------------------------------------------------------------------``
## PoC: Balance oracle is not updated regularly

### Pattern
TWAP implementation issue

### Root Cause
The balance oracle was not updating the price frequently

### Assumption
The price is updated regularly in balancer oracle

### Broken Invariant
The price is updated 

### Attack Story
The price is updated after swap function => not so much trasaction => window is small => which mean the price is not updated regulary => the attacker can use this time period => when the eth price in market is low => and high in this oracle due to no updation of price .

### Checklist
- When does the oracle update?
- Is the assumption valid?
- Can the returned price become incorrect?

### Mitigation
use the good oracle resource like : chainlink

``-----------------------------------------------------------------------------------------------------------------------------``
## PoC: Uniswap price can be Manipulated

### Pattern
Multiple Hopes + low Liquidity + small window  

### Root Cause
Due to Low lqiuidity and using such collateral with multiple pair with addition of small TWAP window lead
to the price manipulation

### Assumption
trusting the Oracle
collateral in Pools are enough to provide the fair price

### Broken Invariant
The Protocol has enough liquidity to provide sufficent liquidity with no price manipulation


### Attack Story
due to less liquidity => attacker can use the large flash loan for temproperay purpose => and manipulate the price => and also take the advantage of small TWAP => then liquidate the provide with profit

### Checklist
- check the TWAP implementation?
- does the window is short?
- does the liuqidity is less pool?
- are both pool and TWAP price is matchinhg ? (according to the market)

⭐ External Market Price
        ≈
Oracle Price (very important) (almost equal)

### Mitigation
- use large TWAP window
- correct TWAP implementation
- observe the liquidity in pool
- compare the external market price and oracle price

```-------------------------------------------------------------------------------------------------------------------------------------------------------```
## PoC: Wrong Method to Calculate the Price

### Pattern
Incorrect LP Valuation

### Root Cause
using the Price methodelogy to calculate the price 

### Assumption
The Orcale will calulate the price 

### Broken Invariant
Borrower > debt

### Attack Story
There is no such profit to attacker but The protocol will go under the high debt /Insolvency .
due to, wrong price method the valutaion of BPT stable price increases more than expected 

### Checklist
the function that provide price , does it check reentrancy ?
- how it calulate the price?
- natspec should match your implementation



### Mitigation
- use different price method 

```---------------------------------------------------------------------------------------------------------------------------------```
## PoC: Mistakely calcualte the value of LP in terms of ETH

### Pattern
Incorrect LP Valuation

### Root Cause
mistakely calculates the value of Liquidity pool in terms of ETH

### Assumption
LP pool return correct value

### Broken Invariant
Healthy position cannot be liquidated

### Attack Story
Attacker can use this to liquidate the Healthy positon and profit it from it

### Checklist
- always check how price is calculated?
- natspec should match your implementation

### Mitigation
- Dont divide by the value of ETH

```---------------------------------------------------------------------------------------------```
## PoC: ERC4626 is vulnerbale to price manipulation

### Pattern
ERC4626Oracle is vulnerable to price manipulation

### Root Cause
using priceRedeem();

### Assumption
ERC6426Oracle provide the valid price

### Broken Invariant
Deposite >= shares

### Attack Story
attacker is flash loan -> change the price in Oracle => previewRedeem changes => oracle read
manipulated price => protocl accept fake colloteral => can borrow large amount of assets  and also can liquidate other

### Checklist
-ERC4626Oracle is used ?
- does it used as ERC4626 oracle?
- does it user any other way to calculate the price?

### Mitigation
- does not use previewRedeem function use TWAP


## PoC: Curve virtual_price Read-Only Reentrancy

**Pattern**
A `view` function gets called mid-transaction, while the contract is only half-updated — so it returns a wrong value.

**Root Cause**
Curve's `remove_liquidity()` sends ETH out *before* updating its internal balances. That gap is the bug.

**Assumption**
Sentiment assumed `get_virtual_price()` always shows the true, settled price — because it's "just a view function."

**Broken Invariant**
`virtual_price` should always reflect a fully-settled pool. It doesn't — for a brief window mid-call, it's calculated from half-old, half-new numbers.

**Attack Story**
1. Attacker deposits ETH into Curve.
2. Attacker calls `remove_liquidity()`.
3. Curve sends ETH back first → attacker's contract regains control before balances update.
4. Attacker calls Sentiment's `RiskEngine` right then — it reads a crashed `virtual_price`.
5. Healthy accounts now look under-collateralized.
6. Attacker liquidates them and keeps the premium.
7. Curve call finishes, price goes back to normal, attacker cashes out.

**Checklist**
- Any view function combining state that updates at different times?
- Is that view function trusted by another contract?
- Can it be read from inside someone else's external call?
- Could a flashloan make the manipulation big enough to matter?

**Mitigation**
- Best: fix Curve to update all balances *before* sending ETH (proper CEI).
- Practical: before trusting the price, check the source isn't mid-reentrancy (e.g. a dummy call that reverts if its own lock is engaged).
- Long-term: use a TWAP instead of a raw spot `view` value for anything this sensitive.

-------------------------------------------------------
## PoC: Pumps are not updated in the shift() and sync function allowing the oracle manipulation

### Pattern
Oracle Manipulation

### Root Cause
internal accounting != well balance

### Assumption
-> shift caller is trusted
-> token is valid (token is whitelisted)
-> recpient is valid
-> amount should greater  than zero

### Broken Invariant
-> internal accounting reserve shoud be equal to the actual token balance at the reserve

### Attack Story
-> attacker use the shift() inorder to shift the token/balance in the  current block 

-> pump ka update behaviour manipulated reserve state ki wajah se exploitable hota hai.

-> then use the swapFrom and swapTo inorder to add the manupilated price in the reserve and tranfer to the asser

### Checklist
-> doe shift() update the pump variable
-> does shift() overwrite the last previous block?
-> does swapFrom and swapTo update the Pump variable.
-> can attacker can manipulate the same block-> with updated price -> and can recover the fund?

### Mitigation
-> update the price `Pump` in sync and shift function

-----------------------------------------------------------------------
## PoC: slot0 can be manipulated

### Pattern
Oracle Manipulation

### Root Cause
The slot price can be manipulated using oracle Manipulation

### Assumption
-> slot0 provide the safe price  and the pool is not manupilated.

### Broken Invariant
-> slot0 provide stale price after falsh inside the Pool.

### Attack Story
when the tick price is between the tick range. this happen using the reallocate() -> which call the other function to get the current slot0 price. and this slot0 could provide the stale price when the attacker manipulate the reserve. checks the current tick range -> swapForOutOfRange() . then update the current position. after that attacker removes the maniputlation. but till then the positon would have changed.

### Checklist
# Spot Price Manipulation — Audit Checklist

Use this checklist whenever you see a protocol reading a DEX price.

## 1. Identify the Price Source

- [ ] Where does the price come from?
  - [ ] Uniswap V2 `reserve0 / reserve1`
  - [ ] Uniswap V3 `slot0()`
  - [ ] `sqrtPriceX96`
  - [ ] `tick`
  - [ ] Another DEX / pool

## 2. Check Whether It Is a Spot Price

- [ ] Is it an instantaneous spot price?
  - [ ] If yes → 🚨 potentially manipulable.

## 3. Check Price Manipulation

- [ ] Can an attacker move the DEX price temporarily?
  - [ ] Is the pool low liquidity?
  - [ ] Is a flash loan available?
  - [ ] Can a large swap move the price significantly?

## 4. Trace How the Price Is Used

- [ ] What does the protocol do with the price?
  - [ ] Liquidation
  - [ ] Rebalance / reallocation
  - [ ] Mint / burn
  - [ ] Collateral valuation
  - [ ] Borrowing limit
  - [ ] Withdrawal / deposit decision
  - [ ] Other financial action

## 5. Look for a State Change

- [ ] Does the price trigger a state-changing action?

```text
spot price
    ↓
condition
    ↓
state-changing action

### Mitigation
-> use TWAP instead of slot0 price.