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
attacker will borrow loan 

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
Oracle Price (very important)

### Mitigation
- use large TWAP window
- correct TWAP implementation
- observe the liquidity in pool
- compare the external market price and oracle price