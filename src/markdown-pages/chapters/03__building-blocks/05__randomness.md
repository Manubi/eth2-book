---
path: "/chapters/building-blocks/randomness"
title: "Randomness: RANDAO"
---

Our lives are highly subject to the many chaotic and random events that occur at every moment. Without randomness, our existences would be entirely predictable and, as a result, not very much fun. We're at the mercy of randomness every time we play the lottery or a game of D&D. We're truly lucky if we're able to say that tomorrow holds unknown possibilities.

Randomness also plays a central role in our technological lives. Our computers use random numbers to keep us safe online. Many modern phones include special-purpose hardware devices dedicated entirely to generating random numbers. We wouldn't be able to create secure Ethereum private keys or encrypt our embarrassing web searches if computers couldn't understand some concept of randomness.

Random numbers are obviously key for many of the things we interact with every day, and Eth2 is no exception. Randomness helps Eth2 run efficiently and securely in the face of constant adversity. We motivate this section by first looking at a few of the ways that Eth2 makes use of randomness. We then explore the concept of randomness in much more detail. Finally, we explain in detail how Eth2 actually generates random numbers.

Although the concept of randomness can be confusing, we try to stay clear of any unnecessary technical details. We think anyone can get a good intuition for the ways that computers and, by extension, Ethereum deal with random numbers.

## Uses for Randomness in Eth2
Randomness keeps the Eth2 trains running on time. Before we explore the general concept of randomness, it's worth getting a feel for the various ways that Eth2 actually makes use of random numbers.

### Block Proposers
Blockchains need block producers. Although there are many ways to produce blocks in a Proof of Stake system, we generally want to maintain a few key properties. First, we want the selection process to be fair to all of the validators. Blockchains generally give out a reward to block producers, so we want to ensure that validators are being paid in proportion to their investment in the system. Our block proposal mechanism should therefore make sure that validators are given the opportunity to produce blocks at a rate mostly proportional to their stake in the system. For example, a validator with 10% of the total stake in the system should produce approximately 10% of all blocks. Simple enough in theory, somewhat difficult in practice.

If each validator has the same amount of stake, then we could just sort the validators into a list (alphabetically, perhaps) and allow validators to produce a block based on their position in this list. We'd get a perfect distribution of blocks between validators every time, no randomness required. We could even extend this system to handle the case that validators have different amounts of stake.

We usually refer to such a system as "round-robin." It's easy to implement, but it's also quite vulnerable to certain types of attacks. Particularly, round-robin selection means that we know the list of block producers far in advance. Any would-be adversary would have plenty of time to strategically attack specific validators in order to manipulate certain blocks. An adversary might try block a validator from the rest of the network in order to prevent that validator from producing a block in a Denial-of-Service attack. Validators could even try to collude with their neighbors on the list to halt the network for a significant period of time by failing to produce blocks.

We want to deter these attacks in Eth2, so we instead use random numbers to pick new block producers during each epoch. As long as the random number generation process is difficult to manipulate or predict, it'll be significantly harder to deny service to or collude with other validators. The law of large numbers also guarantees that the distribution of blocks will, over time, generally reflect the amount of stake owned by each validator.

### Committees
Eth2 relies on the "committees," defined groups of validators, to perform certain actions like voting on the validity of blocks within shards. Committees are particularly important to the security of Eth2's sharding system. If an attacker can gain control of 2/3rds of a committee voting on the validity of a shard block, then they can cause an invalid shard block, perhaps one that generates ETH out of thin air, to be considered valid by the rest of the network.

Although Eth2 does allow users to submit "fraud proofs" in these events, the Beacon Chain would still be forced to roll itself back to a point before the invalid shard block. This roll-back would be a major service disruption, so we need a good source of randomness that reduces the probability of this sort of event.

### Applications
Many blockchain applications also need good sources of randomness. For example, the various lottery applications already running on Eth1 are only as good as their mechanism for generating random numbers. So far, applications have typically either relied on relatively poor sources of randomness, like block hashes, or attempted to roll their own versions of the random number generation process used in Eth2. We don't want application developers to have to build complex protocols for primitives like randomness, so it's important that Eth2 provides a secure and accessible source of randomness.

## Understanding Randomness
We've spoken a lot about randomness so far, but we haven't really discussed a clear definition of the term. We might have an intuition for the meaning of the word "random," but most of us would probably have a difficult time explaining the concept in detail.

Randomness is interesting because some of its forms are simply "better" than others. A dictionary might describe randomness as something that has a quality of unpredictability, fitting for the highly chaotic experiences of human life. However, certain things appear to be "more" random than others. Weather patterns can seem very random, but we're often able to predict them to some extent. Lottery outcomes are pretty random, but ask anyone to pick a random number between one and ten, and they're probably going pick seven.

The idea of true unpredictability breaks down further in the context of the computer. Computers are usually meant to execute programs in expected, or deterministic, ways. We typically want our programs to generate the same output when given the same input. Our lives would be much more difficult if our computers started behaving in completely "random" ways. 

Our computers do still, however, rely on random numbers for a lot of important functionality. For example, computers need access to random numbers in order to generate Ethereum private keys. Otherwise, anyone else could easily reproduce the behavior of our computer and, therefore, reproduce our private keys. Clearly there must be some way for computers to generate "randomness." 

We know, however, that computers cannot truly generate random numbers on their own. Instead, computers rely on reasonable unpredictable external inputs. For instance, a program might derive random numbers from the movement of your mouse over a period of time. This method is effective because mouse movements are generally very unpredictable. It's extremely unlike that any two people have ever moved their mice across their screens in exactly the same way over a non-trivial period of time. Furthermore, cursors can take so many potential paths across a screen that it's infeasible for an algorithm to guess which movements you used to generate a given random number.

Blockchains face even more problems when attempting to generate randomness. Ethereum is somewhat like a computer, so we could start by trying to make use of a similar input-based method of generating random numbers. In order to achieve this, we somehow need to get these external inputs into Ethereum. We could assign someone to be responsible for creating and publishing random numbers, but they might just pick specific numbers that, for example, cause them to win an Ethereum-based lottery system. We instead need some sort of "decentralized" randomness that can't easily be manipulated for individual gain.

## RANDAO
RANDAO is a relatively simple mechanism for generating "random" numbers in a decentralized way. The core idea behind RANDAO is to have a large group of people come together to generate a random number instead of simply trusting one person to do it on everyone's behalf. One simple version of RANDAO is simply to have each member of a group come up with a random number on their own. We can then take these random numbers and "mix" them together in a way that each person's number has an equal impact on the final result.

There's a flaw in this simple version of RANDAO if members of the group can see other members' numbers before they pick their own. The last person to add their random number to the mix could simply look at the current value and pick a "random" number that, when mixed into the current value, gives a favorable outcome. An attacker could run through many potential outcomes to find that "random" number that gives a good result. Obviously, this wouldn't really be a useful "random" number for any practical purposes because the attacker could always cheat.

We generally solve this problem by requiring that everyone "commit" to a random number in advance. These "commitments" are cryptographic guarantees each user makes in advance. Then, when the it's time for everyone to reveal their random numbers, they can only reveal the number that they committed to. This means it's no longer possible for an attacker to pick any number they want, since they already committed to a number earlier on.

This improved version of RANDAO is more resistant to attacks, but there's still one problem. The last revealing user could, if they want, simply refuse to publish their original random number. Although this doesn't give the last user effectively infinite attempts to change the output, it does allow them to roll the dice one more time. Instead of getting one fixed result, such an attacker could basically choose between two different results. Generally speaking, the last `n` members could collude to get an additional `2^n` rolls of the dice. Obviously this isn't ideal, as the randomness is somewhat subject to bias, but we have some guarantees about the potential outcomes of these results depending on the amount of the system owned by an attacker.

## VDFs
We know that RANDAO isn't perfect, but we want to see if it's possible to improve upon it. Some interesting new developments in Cryptography have come where we have the idea of VDFS or verifiable delay functions. The idea behind VDFs is that you are doing a mathematical computation that you must do in serial and cannot parallelize, but that spits out a proof that you did the computation that can be verified in a short period of time. This is sort of coming out of the idea of time-lcok crypto, except that you now have an efficient proof. 

So why do we care? well, they giev us a cool thing if we combine it with RANDAO. The idea is that instead of just waiting for the reveals Or not, we wait for the last reveal and then we use the last value as an input to a VDF. This VDF takes a long time to finish, but then spits out the actaul random number that we will use. The point here is that let's say the attacker has the last validator and can choose whether to reveal or not. However, in order to see how the reveal or not will actually influence the random number, the attacker would have to compute the VDF. This takes longer than 1 slot time, so the attacker doesn't gain any advantage by not revealing. Cool!

One issue with VDFs is basically the advantage proportion. Similar to PoW difficulty, VDFs can be tuned to take a ceratin amount of time to compute the result. This takes much less resouces than PoW of course, but the attacker could get an advantage back if they figure out how to significantly decrease the tme to compute ther esult compared to everyone else. As a result, there's a lot of work going on about getting this advantage down to a minimum, and producing a low-cost ASIC that people could buy (just one per validator!) that would ensure the VDF advantage is minimized. 