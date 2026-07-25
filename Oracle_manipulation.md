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
Healthy position will liquidate due to incorrect pricising

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
attacker is flash loan -> chage the price in Oracle => previewRedeem changes => oracle read
manipulated price => protocl accept fake colloteral => can borrow large amount of assets  and also can liquidate other

### Checklist
-ERC4626 is used ?
- does it used as ERC4626 oracle?
- does it user any other way to calculate the price?

### Mitigation
- does not use previewRedeem function use TWAP
