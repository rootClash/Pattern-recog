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
