# smart-contract-security-guidelines

(Not only for EVM)

#### Care about flash loans: never assume a contract caller's current balance is a long-term balance

Flash loan can offer a large number of tokens at little costs. For example, anyone may borrow more than 50% of the total supply of a governance token and execute malicious proposals. 

#### Ensure our benefits first; Set local state first

This is to prevent reentrancy attack. The common lesson from `the DAO` taught us to set the local environments in our contract before calling another contract. But additionally, for example, we should first transfer a user's balance to our contract, then transfer ours to the user. 

#### Preventing denial of service

If in a contract method you set something that may globally affect the contract's service, and in that method you call an untrusted contract (or send token to someone), then the called contract can always throw some exceptions, preventing your method to be executed. 

#### Throwing an Exception may not stop the contract

Your Exception can be caught by the caller of the contract. (Though EVM has only one type of safe exception "Out of GAS")

Do not write anything or call any untrusted contract before you throw an Exception. 

