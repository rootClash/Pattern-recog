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