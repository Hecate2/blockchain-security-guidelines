# Smart contract layer

(Not only for EVM)

#### Care about flash loans: never assume a contract caller's current balance is a long-term balance

Flash loan can offer a large number of tokens at little costs. For example, anyone may borrow more than 50% of the total supply of a governance token and execute malicious proposals. 

#### Ensure our benefits first; Set local state first

This is to prevent reentrancy attack. The common lesson from `the DAO` taught us to set the local environments in our contract before calling another contract. In other words, do not read or write the storage after an untrusted call, because the untrusted call may change the storage to create obsolete/unreasonable values. But additionally, for example, we should first transfer a user's balance to our contract, then transfer ours to the user. 

#### Preventing denial of service

If in a contract method you set something that may globally affect the contract's service, and in that method you call an untrusted contract (or send token to someone), then the called contract can always throw some exceptions, preventing your method to be executed. 

#### Throwing an Exception may not stop the contract

Your Exception can be caught by the caller of the contract. (Though EVM has only one type of safe exception "Out of GAS"; Neo has isolated snapshots of the execution of different smart contracts, thus avoiding the problem)

Do not write anything or call any untrusted contract before you throw an Exception. Also, consider re-entrancy into your contract where you had thrown an exception. 

#### Check whether the caller is a contract

Be extremely careful when you are writing some random stuff. If the caller is a contract, the caller can know the accurate block timestamp, and whether there is a certain transaction included in this block before or after the current transaction, etc. 

#### Check the input of cryptography functions

This sounds like an unbelievable type of mistakes, but such mistakes do occur. Do not use constant input. Do use the input generated by a trusted party (and certainly not the user).

#### Hash collision!

EVM identifies things by hash, instead of name.

#### delegatecall, staticcall, callcode, call... (for Solidity)

`delegatecall` runs another contract in the storage environment of the current contract (the calling contract), and changes the storage of the calling contract. The `msg.sender` is the one who launched the transaction. 

`callcode` works like `delegatecall`, but `msg.sender` is set to the calling contract which initiates this `callcode`. 

`staticcall` is only available in inline assembly codes. It can only call `view` or `pure` functions of another contract in the storage environment of the called contract. `view` functions cannot write the storage, and `pure` functions can neither write nor read the storage. 

`call` simply runs the called contract in the storage environment of the called contract. The storage of the called contract may be written. 

**Be extremely careful when you mix all kinds of calls. There can be critical risk of re-entrancy.** 

#### Better have the verification logics executed only once in one transaction

If the verification can be executed for many times in a single transaction, usually what is being verified is varying. There is much higher risk that what you are verifying is not exactly what you want. 

For example: You are going to verify `msg.value == input_ETH_amount` for many times in the same transaction. Then an attacker can pass your verification many times to let you think the attacker has many ETH, by sending only 1 ETH.

#### Be careful when using global variables in a loop

Dangerous!

#### Get familiar with all the data types you use

And execute the common best practices to prevent underflow and overflow!

#### Safe components do not automatically bring a safe system

Read codes using depth-first search, instead of breadth-first search.

# P2P network layer

#### Only relay verified payloads

We do not need a flood of rubbish packages being relayed by naïve honest nodes! Relaying only verified payloads prevents someone utilizing your P2P network as an instant messaging or bittorrent software, or just cast a broadcast storm attack. 

#### Send payloads passively

Do not broadcast something that you just received. Anything sent by codes may lead to broadcast storms. It's safer to send payloads with careful bandwidth limit on idempotent `GET` requests. 

#### Verify the source IP address before responding

Make sure the source of the connection is exactly that indicated in the incoming message. An easy way is to use TCP, in case you get utilized for a Reflection Amplification DDOS attack. 

#### Close a connection only on verified request

Still you should carefully verify the source IP address which sent you something that let you decide to close the connection. In blockchain networks your connection to a remote host may be known by a third party. 

#### Your connection may be listened by a third party

Surely you do not have mechanisms like `HTTPS`. You might be talking to a third party which retransmits your messages to your designated target. You may be even talking to yourself if the third party decide to modify and retransmit messages to yourself. 

# Garbage Collection

There have been many denial-of-service vulnerabilities related to garbage collection codes of common programming languages. And such vulnerabilities on the blockchain may lead to total failure of all nodes if vicious smart contracts are executed by an attacker.