## PoC: Divison before calculation

### Pattern
Rounding Error

### Root Cause
The protocol is a vault which has a functionality of reawarding the validators. it use the formula 
```validatorCounts[idx] * (_reward / totalActiveValidators) shares. Such share calculation suffers from a rounding error caused by division before multiplication.```
inside the function _mintRawShares() was uesd to calculate it. But as it is using , divison before multiplication it creates the rounding error.

### Assumption
reward already collected by the vault
block.timestamp > lastRewardTimestamp

### Broken Invariant


### Checklist
-> focus on math of DEFI that is important
-> test those formula

### Mitigation
-> multiply before division = (validatorCounts[idx] * _reward ) / totalActiveValidators

