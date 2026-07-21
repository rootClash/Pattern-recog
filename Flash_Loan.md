## PoC: Oracle can be Manupilated

### Pattern
Oracle Manupilation

### Root Cause
Due to Flash Loan 

### Assumption
Oracle will Provide the market value

### Broken Invariant
price derived from reserves must reflect true market value"

### Attack Story
attaker use the flashLoan => Increase the price of one token => again take borrow more collateral from lending protocol => lending protocol provide that high amount due to increase price in reserve => again swap back the Quaote amount (another token) => bring the base asset to the same value.=> pays back the DAI to the reserve 

### Checklist
- check whether flash loan can be done or  not?
- whether  using the pool price or TWAP or any other external oracle?

### Mitigation
- use TWAP , oracle 
- break the code as a devloper.

``---------------------------------------------------------------------------------------------``
## PoC: Different Spot price

### Pattern
Spot price manipulation

### Root Cause
wrong comparison between the reserve and the oracle price calculation

### Assumption
reserve will provide and calculate the market value and will be same as external oracle price

### Broken Invariant
spot price from reserve == oracle reserve price

### Attack Story
no such attack it will just give error

### Checklist
- does the calculation is proper in reserve?

### Mitigation
- The documentation (comment) and the implementation must agree.
(thats  what was missing as the result the function were not same)

``----------------------------------------------------------------------------------------------------------``
## PoC: Desgin Issue

### Pattern
Flash Loan + Desgin issue + Access control

### Root Cause
inside the `urgentRedemption` attacker can pass any Tove Id of higher Indivisual Collateral Ratio.

### Assumption
1. Trust on parameter input passed by the attacker
2. Branch must be shut down
3. Attacker must hold BOLD token which act like a stableCoin when user deposit
4. Profitable Condition

### Broken Invariant
value of collateral decrease in vault == value of decrease debt

### Attack Story
attacker tooked the falsh loan from uniswap => USDT -> to bold using using `urgentRedemption` for sUSDe by adding higher ICR ids in array => sUSDe -> USDT then with 2% BONU that sUSDE us conveted into USDT using curve => repay the flash loan => and the left USDT to WETH => then WETH to ETH => forward to attacker.

### Checklist
1. Access control in important function?
2. Desgin issue ?
3. bonus against the oracle price ?
4. can the function can be flashLoaned?
5. Does market price and collateral reserve price differ huge?


### Mitigation
1. access control
2. no bonuse according to the current market
3. Force the user to redeem against the lowest ICR..





