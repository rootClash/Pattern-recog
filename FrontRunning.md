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


## PoC: Due to Plugin revert the slashing could not be initated.

### Pattern
state change front running

### Root Cause
Due IPlugin(plugin)._notifyStakeChangeAllPlugins() revert the slashing get reverted.

### Assumption
-> The IPlugin(plugin)._notifyStatkeChangeAllPlugins() will notify other plugins about the state change.
-> . A registered plugin can change its internal state before
   the slashing transaction executes.
-> The target validator/account is genuinely eligible for slashing
->The validator has a previously initiated withdrawal that can
   become executable after the slashing attempt.

### Broken Invariant
-> All plugins that require notification must be notified when a stake change occurs.

### Attack Story
1. attacker initate the withdraw request.
2. the slasher add the address
3. attacker front run that 2nd request by adding the tx of changing the _noftufyStakeChangeAllPlugins state and front run it
4. once slash() function get reverted
5. attacker add another tx by changing the notify stake update of plgins
6. then again at the small window , attacker withdraw the amount requested in step 1

### Checklist
[] check who control the external call?
[] what if the external call get reverted?
[] can the attacker control the external call and front run it?

### Mitigation
use try and catch in the external call (vip)


## PoC: The `exchangeRateStored()` function allows front-running on repayments Union Finance Update

### Pattern
FrontRunning

### Root Cause
The attacker overseved the `totalRemedeable` state how it is changing in `_repayBorrowFresh()` in addition of it `exchangeRateStored()` ,`mint()`,`redeem()` also get affected.

### Assumption
-> Interest is split between protocol reserves and redeemable amount
-> toRedeemableAmount <= interest
-> totalRedeemable represents the portion of interest allocated to uToken holders.

### Broken Invariant
no invairnt has broked

### Attack Story
-> attacker mint the amout before the awaited tx with large repayment amount
-> attacker put tx before the pending tx (front run) [totalRedeem increases]
-> attacker then redem the amount because that [totalRedeem] increases in redeem function.

### Checklist
[]observe the state change in all the function
[]does any state change is occuring in most of the function (observe that)
[]does the forntrunning the function can encomical help the attacker

### Mitigation
An approach could be implementing TWAP in order to make front-running unprofitable in this situation. but this is not a proper remedation of front running However that profit attacker can be reduce in most excent.