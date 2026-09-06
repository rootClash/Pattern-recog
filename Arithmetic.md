## PoC: Repetated Bad debt lead to the accounting overflow

### Pattern
Repetated debt -> shares inflanation -> arithmetic overFLOW

### Root Cause
due to the bad debt of the borrower , protocol liquidate the borrower asset in shares and add to the protocol as the result . totalShares increases and  totoal assset decreases. In the end the shares per token gets inflanated. 

### Assumption
-> Under normal deposits, the shares-per-token ratio remains constant
-> Borrower is authorized
-> no overFlow in the ratio of totalshares / totoalToken

### Broken Invariant
-> shares-per-token is not same in any condtion.

### Attack Story
NO SUCH ATTACK VECTOR
### Checklist
-> does the share-per-token reamin constant all the time?
-> does the share-per-token value increases?
-> does shares increases after liquidation?
-> is there any adjustment factor used to normalize the shares.
-> does any bad debt is created? and how it is resolved?

### Mitigation
-> use the ADJUST FACTOR to normalize the large shares  but also accept that the precision loss is acceptable
