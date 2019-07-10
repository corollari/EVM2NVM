# EVM2NVM
> A journal of my adventure trying to create a compatibility shim between the Ethereum Virtual Machine and the NEO Virtual Machine

At first it would seem that creating a compatibility shim between these two machines is possible, given that they are both stack based, they share the same basic architecture (and even quite a few opcodes) and both are Turing complete, so theoretically it is possible to emulate any of these two in the other one.

Encouraged by these ideas I started delving into the inner workings of both to see how it could be done. 

I failed terribly.

Here is a list of all the problems I found (there's probably more but I stopped looking when it was clear that this endeavour had no end in sight) so that if someone else tries to do the same later on they can pick up where I left (or just give up before spending some time to deteremine that it is simply not possible):

- `CREATE` opcode functionality can't be replicated in NEO. The reason for that is that in NEO the address of all contracts  deployed is based on their script hash, therefore it is as if all the contracts were deployed using `CREATE2`. Simulating a `CREATE` deployment would require being able to create a script which hashes to the same hash as the one obtained from hashing a combination of the creator's account and a nonce. The creation of such script is totally contrary to the nature of cryptographic hash algorithms, as it would mean a pre-image attack is possible on that hash algorithm.
- Contracts in NEO cannot send NEO or GAS, therefore `CALL` and all the other calling opcodes cannot be replicated in NEO, as a non-zero number in their `value` field would require the sending of funds to another address from the smart contract.
- bn128 elliptic curve cryptography should be possible to replicate in NEO but would require a lot of computation, making the smart contracts using it too expensive to call.
