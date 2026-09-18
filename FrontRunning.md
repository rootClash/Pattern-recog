## PoC: First Depositor Can Front-Run and Steal the Next User's Stake

### Pattern
FrontRunnning + Vault manipulation + Rounding Error

### Root Cause
The asset price in vault can be manipulate and result into rounding error price of shares. Beyod that the attacker can perform frontrunn attack in the mempool.

### Assumption
-> The exchange rate/share price is calculated correctly based on the current underlying assets and share supply.
-> Exchange rate accurately reflects the pool's assets
   relative to outstanding shares.
->  Exchange rate should not be manipulable immediately
   before a user's deposit.
-> Rounding should not allow a meaningful deposit
   to mint an extremely small number of shares.


### Broken Invariant
The exchange rate/ share price is manipulated based on the current Underlying assets . because inside the (totalStaked + totalReward) the totalReward can be added using the addReward()as the result the rate / share price deacreases.

### Attack Story
-> Attacker first deposit into vault.
-> User deposit the asset
-> attacker add its tx before the user with addReward()
-> User tx execute now
-> The user get the share at the high price after the vault price manipulation
-> then attacker use reddem the asset with profit

### Checklist
-> can attacker obeserve the pending transaction?
-> can attacker watch the relevant and important parameter?
-> Does attacker tx can modify the state of user dependent state?
-> can attacker submit the tx before the user tx?
-> is the attacker repetable/economically practical?
-> Does attacker gain value because they execute first?

### Mitigation
-> prevent the rate/share exchange manipulation
-> check the rounding error issue
-> add the virtual shares so that exchange rate cannot be manipulated