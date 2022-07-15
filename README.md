# Smart contract layer

(Not only for EVM)

#### Care about flash loans: never assume a contract caller's current balance is a long-term balance

Flash loan can offer a large number of tokens at little costs. For example, anyone may borrow more than 50% of the total supply of a governance token and execute malicious proposals. 

#### Ensure our benefits first; Set local state first

This is to prevent reentrancy attack. The common lesson from `the DAO` taught us to set the local environments in our contract before calling another contract. But additionally, for example, we should first transfer a user's balance to our contract, then transfer ours to the user. 

#### Preventing denial of service

If in a contract method you set something that may globally affect the contract's service, and in that method you call an untrusted contract (or send token to someone), then the called contract can always throw some exceptions, preventing your method to be executed. 

#### Throwing an Exception may not stop the contract

Your Exception can be caught by the caller of the contract. (Though EVM has only one type of safe exception "Out of GAS"; Neo has isolated snapshots of the execution of different smart contracts, thus avoiding the problem)

Do not write anything or call any untrusted contract before you throw an Exception. 

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