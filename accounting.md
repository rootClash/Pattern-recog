## PoC: Accounting issue

### Pattern Accounting mismatch due to fee not reflected in shares

### Root Cause
Not including the fee inside with actual amount to liquidate

### Assumption
collateral amount is already calculated 
Fees is already set 
user is liquidateable

### Broken Invariant
Fee + acutal amount is removed from the totalsupply
Total user claims ≤ actual pool assets

### Attack Story
no such attacking vector : but di=ue to this issue the protocol can lead to 
insolvency

### Checklist
-> which asset is removed from the pool and also check does that asset is removed from the 
protcol accounting
eg : amount mein feess included hai ki nhi

### Mitigation
Liquidation fee ko bhi share-burning calculation mein include karo.
ℹ️ Exact fix protocol ke intended economics par depend karega.

