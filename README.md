# ChiaFarms

This repo was contains my fork for the first [Chia Developer Challenge](https://www.chia.net/2022/01/18/learn-chialisp-with-clovyr.en.html). I wrote this file after hours of coding in Chialisp, so please let me know if you spot any mistakes.

TL;DR for judges: There's Chialisp code in the 'code' folder, but, unfortunately, it has not been tested.

## Background

### Chia
Unlike Ethereum, [Chia](https://www.chia.net/) uses the coin set model. There are no 'contracts' - instead, coins are locked by puzzles which dictate how they are spent. This is a good time to shill [my Twitter thread on this topic](https://twitter.com/yakuh1t0/status/1484567282848313351?s=20&t=oXQwDLFcMzIROAWPFeAu0Q). Another aspect that is worth mentioning is that puzzles are built using a LISP-like language called [Chialisp](https://chialisp.com/).

### Singletons
By default, coins do not have any state. They have a parent, a puzzle, and can be spent by providing a solution. The `Singleton` puzzle is used to give a series of coins a unique id that can be easily checked. It's very useful for things like NFTs.

Each time the puzzle hash updates / the singleton is spent, a new coin is created, thus limiting the amount of 'updates' for one singleton instance. Much like the UTXO model, stateful 'contracts' are highly discouraged - more specifically, contracts that would require lots of transactions.

More on Singletons can be found [here](https://chialisp.com/docs/puzzles/singletons).

### CATs

[Chia Asset Tokens](https://chialisp.com/docs/puzzles/cats) are the ERC20 equivalent of Chia. Unlike Ethereum, however, these assets are not held in a 'central contract' that tracks users' balances. Instead, CATs use a puzzle that enables basic transactions between users and much more. Issuance is controlled by a puzzle known as the TAIL which dictates how coins can be minted and burned.

### Offer Files

For me, this is the most exciting feature of Chia. Thanks to a lot of things that just come together in an elegant way, people can generate offers offline. Accepting and cancelling them requires only 1 interaction with the blockchain - it's something that I've never seen before and the potential seems huge. Offers have already been used to swap Chia and CATs in a trustless manner and they are supported by the Chia Light Wallet.

## The Game

ChiaFarms is a simple play-to-earn game designed to be fun to play while helping its developer (me) solidigy his understanding of Chialisp. The main rules are as follows:
 * Players can start farms by paying 10 Farm Bucks (referred to as BUCKs or bucks from now on).
 * Players can also buy seeds, which they can plant on their land
 * After 6 hours, seeds become plants and start producing more seeds (each plant produces 1 seed every hour)
 * Seeds can be converted 1:1 to bucks
 * Bucks can be used to upgrade farms
 * Max farm level is 13
 * For each farm upgrade, the cost for upgrading to the next level doubles
 * For each farm upgrade, the number of land spots doubles and is then incremented by one

Farms are Singletons and have owners. BUCKs and SEEDs are both CATs and can be traded freely between players.

Interesting things that happen in the code:
 - `farm.clsp` holds an ownable puzzle that only updates according to some rules
 - `farm_state.clib` contains all the methods that have to do with the `state` object which defines a farm.
 - announcements use a special 'code' to communicate. For each message, there's a string defined in `announements.clib`. An announcement also contains the amount of tokens that need to be minted/burned and, if the target is a TAIL, a coin id.
 - for each mint/burn action, both puzzles generate an announcement and assert another one. This was inspired from `p2_singleton`.
 - look at the commits to see just how fast I created this file
 - BUCKs and SEEDs can be changed 1:1

## To Mention
A few thing that I would like to mention:
 * While writing this, I just realized that `FARM_INNER_HASH` from the two CATs can't be curried in since it changes. It could be provided as an argument, but that would be insecure. Instead, the standard `FARM_MOD_HASH` should be curried alongside static arguments for the game and then the dynamics one should be accepted into the solution. Time's almost up, so I mentioned this here: it's solvable.
 * The code has not been tested. My experience tells me that it contains a lot of errors. I consider it to be a 'draft' from which I could start building out the real project (I probably won't)
 * Finding resources about testing code using Python was pretty hard. I settled on the nft companion app from xch.gallery, but I didn't get the chance to write my own tests. Just a suggestions.
 * Last thing: thanks! I feel like I learned a lot in the last ~14 hours and the challenge really motivated me to push myself and learn a lot more (I have to admit that I fell a little bit behind after CATs were released). Clovyr, you rock. Chia, you will soon be the definition what 'to rock' means.
