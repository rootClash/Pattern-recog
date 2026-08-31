# PoC: Protocol Assumes ERC20 Transfers Are 1:1

## Pattern
The protocol assumes that the amount requested in `transferFrom()` is equal to the amount actually received by the contract. This assumption breaks for fee-on-transfer and other non-standard (weird) ERC20 tokens.

## Root Cause
The contract records the bid amount without verifying the actual number of tokens received after the transfer. As a result, its internal accounting can differ from the contract's actual token balance.

## Assumption
The contract assumes that `transferFrom(amount)` transfers exactly `amount` tokens to the contract.

## Broken Invariant
The protocol's recorded bid amount should always equal the actual token balance received by the contract.

## Attack Story
1. Alice places a bid using a fee-on-transfer ERC20 token.
2. The contract records the full bid amount.
3. Due to the transfer fee, the contract receives fewer tokens than expected.
4. Later, Alice withdraws her bid or the auction is cancelled.
5. The contract attempts to transfer back the recorded bid amount.
6. Since the contract never received that many tokens, the transfer reverts.
7. As a result, users are unable to recover their funds and the auction flow is disrupted.

## Checklist
- Does the protocol assume ERC20 transfers are always 1:1?
- Is the actual amount received verified after `transferFrom()`?
- Does the protocol support fee-on-transfer, rebasing, or other non-standard ERC20 tokens?
- Is the protocol's accounting based on the requested transfer amount instead of the actual amount received?

## Mitigation
- Verify the actual amount received by comparing the contract's token balance before and after `transferFrom()`.
- Update the protocol's accounting using the actual amount received. (VIP)
- Alternatively, explicitly reject unsupported ERC20 tokens such as fee-on-transfer tokens.

``----------------------------------------------------------------------------------------------------------``
# PoC: Protocol Assumes ERC20 Transfers Are 1:1

## pattern
The protocol assumes that `transferFrom` does not cut fees and value are pegged 1 : 1

## Root Cause
using the weird ERC20 token which collect fees

## broken Invariant
token received == token transfered

## Attack story
1. Attacker depoit the amoun and the transfer the token.
2  In the same function the reward is set on the basis of amount parameter through function.
3. when the user claim the reward , the function received (amount - fee) from the and the reward was set on the
user input. 
4. so user could not able to claim the rewards.

## Checklist
- Does the function use ERC20 token transfer?
- Does internal accounting before and after are done?
- Does protocol support fee-on-transfer, rebasing, or other non-standard ERC20 tokens?

## Mitigations
- Verify the actual amount received by comparing the contract's token balance before and after `transferFrom()`.
- Update the protocol's accounting using the actual amount received. (VIP)
- Alternatively, explicitly reject unsupported ERC20 tokens such as fee-on-transfer tokens.

``----------------------------------------------------------------------------------------``
# PoC: Approval checking is different in some ERC20

## pattern
non standard token

## Root Cause
approval check in USDC is different. in that first allowance should be set 0

## broken Invariant
Approval logic is same : all erc are same

## Attack story
1. set the token for reward
2  but user approved the allowance to someone with some value (not zero)
3. But the token was USDC and the approval in that token is different 
4. the user cannot able to claim the reward.


## Checklist
- What type of ERC20 token can be used?
- Does forceApprove() is used?
- Does it first reset allowance?
- Is the protocol allowlisting tokens?
Search Patterns

Whenever auditing, grep for

approve(
safeApprove(
forceApprove(
increaseAllowance(
decreaseAllowance(
## Mitigations
- Use SafeERC20's forceApprove method instead to support all the ERC20 tokens.

-----------------------------------------------------------------------
## PoC: no check for quoteAmount before tranfer

### Pattern 
WEIRD ERC20

### Root Cause
-> no proper check after seding and reciveing the token 
-> no check for the quote Amount

### Assumption
-> proper check of quote amount

### Broken Invariant
-> token send != token receive

### Attack Story
-> Alice use the rebase token for creating the auction
    -> rebase token ka balance time ka sath change hota hai
    -> Extra accured amount contract mein stuck ho jata hai

-> Bob use the fee base token 
    -> Bob 1 token bhejta hai , contract ko fee cut hone ka baad milta hai
    -> Contract phir bhi 1 token ki accounting karta hai → withdrawal problem.

### Checklist
-> does the amount send is received by the contract
-> does the token used is WEIRD ERC20

### Mitigation
-> its a creater reposibility to take accountibility
-> the amount send == amount recived by the contract address (two seperate accounting should be done)
-> amount transfered should be check before
-> only allowed and whitlested token should be used

