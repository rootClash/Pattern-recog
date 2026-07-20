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

