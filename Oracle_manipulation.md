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