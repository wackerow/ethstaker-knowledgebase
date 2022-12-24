# Staking glossary

* [Attestation](staking-glossary.md#attestation)
* [Attestation aggregator](staking-glossary.md#attestation-aggregator)
* [Beacon chain](staking-glossary.md#beacon-chain)
* [Block](staking-glossary.md#block)
* [Block proposer](staking-glossary.md#block-proposer)
* [Block status](staking-glossary.md#block-status)
  * [Proposed](staking-glossary.md#proposed)
  * [Scheduled](staking-glossary.md#scheduled)
  * [Missed/skipped](staking-glossary.md#missedskipped)
  * [Orphaned](staking-glossary.md#orphaned)
* [Canonical chain](staking-glossary.md#canonical-chain)
* [Chain head](staking-glossary.md#chain-head)
* [Checkpoints](staking-glossary.md#checkpoints)
* [Committees](staking-glossary.md#committees)
* [Consensus layer](staking-glossary.md#consensus-layer)
* [Deposit contract](staking-glossary.md#deposit-contract)
* [Effectiveness](staking-glossary.md#effectiveness)
* [Epoch](staking-glossary.md#epoch)
* [Execution layer](staking-glossary.md#execution-layer)
* [Finalization](staking-glossary.md#finalization)
  * [Finality issues](staking-glossary.md#finality-issues)
* [Fork](staking-glossary.md#fork)
* [Genesis block](staking-glossary.md#genesis-block)
* [Head vote](staking-glossary.md#head-vote)
* [Inactivity leak](staking-glossary.md#inactivity-leak)
* [Inclusion distance](staking-glossary.md#inclusion-distance)
* [Input data](staking-glossary.md#input-data)
* [Justification](staking-glossary.md#justification)
* [Light clients](staking-glossary.md#light-clients)
* [MEV](staking-glossary.md#mev)
* [Mempool](staking-glossary.md#mempool)
* [Participation rate](staking-glossary.md#participation-rate)
* [Peers](staking-glossary.md#peers)
* [Priority fees](staking-glossary.md#priority-fees)
* [Proof of stake (PoS)](staking-glossary.md#proof-of-stake-pos)
* [Signing](staking-glossary.md#signing)
* [Slashable offenses](staking-glossary.md#slashable-offenses)
  * [Attestation violation](staking-glossary.md#attestation-violation)
  * [Proposer violation](staking-glossary.md#proposer-violation)
* [Slasher node](staking-glossary.md#slasher-node)
* [Slot](staking-glossary.md#slot)
* [Source vote](staking-glossary.md#source-vote)
* [Staking deposit CLI](staking-glossary.md#staking-deposit-cli)
* [Suggested fee recipient](staking-glossary.md#suggested-fee-recipient)
* [Sync committee](staking-glossary.md#sync-committee)
* [Target vote](staking-glossary.md#target-vote)
* [Validator](staking-glossary.md#validator)
  * [Eligible for activation & Estimated activation](staking-glossary.md#eligible-for-activation--estimated-activation)
  * [Unique index](staking-glossary.md#unique-index)
  * [Current balance & Effective balance](staking-glossary.md#current-balance--effective-balance)
* [Validator lifecycle](staking-glossary.md#validator-lifecycle)
  * [1. Deposited](staking-glossary.md#1-deposited)
  * [2. Pending](staking-glossary.md#2-pending)
  * [3. Active validator](staking-glossary.md#3-active-validator)
  * [4. Slashing validator](staking-glossary.md#4-slashing-validator)
  * [5. Exiting validator](staking-glossary.md#5-exiting-validator)
* [Validator pool](staking-glossary.md#validator-pool)
* [Validator queue](staking-glossary.md#validator-queue)
* [Validator seed phrase / mnemonic](staking-glossary.md#validator-seed-phrase--mnemonic)
* [Withdrawal address](staking-glossary.md#withdrawal-address)

***

## Attestation

Votes by [validators](staking-glossary.md#validator) which confirm the validity of a [block](staking-glossary.md#block). At designated times, each validator is responsible for publishing different attestations that formally declare a validator's current view of the chain, including the last finalized [checkpoint](staking-glossary.md#checkpoints) and the current [head of the chain](staking-glossary.md#chain-head).

Every active validator creates one attestation per [epoch](staking-glossary.md#epoch) (\~6.5 minutes), consisting of the following components:

| Component                                                                          | Description                                                                                                                                                                                                                  |
| ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Committee**                                                                      | A bitlist of validators where the position maps to the validator index in their committee. The value (0/1) indicates whether the validator signed the data (i.e. whether they are active and agree with the block proposer). |
| **Slot**                                                                           | The slot number that the attestation references.                                                                                                                                                                             |
| **Index**                                                                          | A number that identifies which committee the validator belongs to in a given slot.                                                                                                                                           |
| <p><strong>Chain head vote</strong></p><p><strong>(beacon_block_root)</strong></p> | The root hash of the block the validator sees at the head of the chain (the result of applying the fork-choice algorithm).                                                                                                   |
| **Source**                                                                         | Part of the finality vote indicating what the validators see as the most recently justified block.                                                                                                                           |
| **Target**                                                                         | Part of the finality vote indicating what the validators see as the first block in the current epoch.                                                                                                                        |
| **Signature**                                                                      | A BLS signature that aggregates the signatures of individual validators.                                                                                                                                                     |

An important component related to effectiveness is the chain head vote. This is a vote the validator makes about what it believes is the latest valid block in the chain at the time of attesting. The structure of a chain head vote consists of the following components:

* Slot - Defines _where_ the validator believes the current chain head to be.
* Hash - Defines _what_ the validator believes the current chain head to be to be.

The combination uniquely defines a point on the blockchain. By combining enough of these chain head votes the Ethereum network reaches consensus about the state of the chain.

[**Source (ethereum.org) ↗**](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/attestations/)\
[**Source (Attestant) ↗**](https://www.attestant.io/posts/defining-attestation-effectiveness/)

## Attestation aggregator

Although the data in each [attestation](staking-glossary.md#attestation) is relatively small, it mounts up quickly with tens of thousands of [validators](staking-glossary.md#validator). As this data will be stored forever on the blockchain, minimizing it is important, and this is done through a process known as attestation aggregation.

Aggregation takes multiple attestations that have all chosen to vote with the same committee, chain head vote, and finality vote, and merges them together in to a single aggregate attestation.

An aggregate attestation differs in two ways from a simple attestation. First, there are multiple validators listed. Second, the signature is an aggregate signature made from the signatures of the matching simple attestations. Aggregate attestations are very efficient to store, but introduce additional communications and computational burdens.

If every validator was required to aggregate all attestations it would quickly overload the network with the number of communications required to pass every attestation to every validator. Equally, if aggregating were purely optional then validators will not bother to waste their own resources doing so. Instead, a subset of validators is chosen by the network to carry out aggregation duties1. It is in their interest to do a good job, as aggregate attestations with higher numbers of validators are more likely to be included in the blockchain so the validator is more likely to be rewarded.

Validators that carry out this aggregation process are known as aggregators.

[_**Source ↗**_](https://www.attestant.io/posts/defining-attestation-effectiveness/)

## Beacon chain

A major part of the work of the beacon chain is storing and managing the registry of [validators](staking-glossary.md#validator) – the set of participants are responsible for running the Ethereum [Proof of Stake (PoS)](staking-glossary.md#proof-of-stake-pos) system.

This registry is used to:

* Assigns validators their duties.
* Finalizes [checkpoints](staking-glossary.md#checkpoints).
* Perform a protocol level random number generation (RNG).
* Progress the beacon chain.
* Vote on the [head of the chain](staking-glossary.md#chain-head) for the fork choice.

[_**Source ↗**_](https://notes.ethereum.org/@djrtwo/Bkn3zpwxB#High-level-overview)

## Block

A block is a bundled unit of information that include an ordered list of transactions and consensus-related information. Blocks are proposed by [Proof of Stake (PoS)](staking-glossary.md#proof-of-stake-pos) validators, at which point they are shared across the entire peer-to-peer network, where they can easily be independently verified by all other nodes. Consensus rules govern what contents of a block are considered valid, and any invalid blocks are disregarded by the network. The ordering of these blocks and the transactions therein create a deterministic chain of events with the end representing the current state of the network.

## Block proposer

A chosen [validator](staking-glossary.md#validator) by the [Beacon Chain](staking-glossary.md#beacon-chain) to propose the next [block](staking-glossary.md#block). There can only be one valid block per [slot](staking-glossary.md#slots).

## Block status

### Proposed

The block was proposed by a validator.

### Scheduled

Validators are currently submitting data.

### Missed/skipped

The proposer didn’t propose the block within the given time frame, so the block was missed/skipped.

### Orphaned

In order to understand this, let us look at the diagram below "1, 2, 3, ... ,9" represent the slots.

1. Validator at slot 1 proposes the block “a”.
2. Validator at slot 2 proposes “b”.
3. Slot 4 is being skipped because the validator didn’t propose a block (e.g.: offline).
4. At slot 5/6 a fork occurs: Validator(5) proposes a block, but validator(6) doesn’t receive this data (e.g.: the block didn’t reach them fast enough). Therefore Validator(6) proposes its block with the most recent information it sees from validator(3).
5. The [fork choice rule ↗](https://notes.ethereum.org/@vbuterin/rkhCgQteN?type=view#LMD-GHOST-fork-choice-rule) is the key here - It decides which of the available chains is the canonical one.

## Canonical chain

The canonical chain is the chain which is agreed to be the 'main' chain and not a [fork](staking-glossary.md#fork).

## Chain head

The latest block received by a validator. This does not necessarily mean it is the head of the [canonical chain](staking-glossary.md#canonical-chain).

## Checkpoints

The [Beacon Chain](staking-glossary.md#beacon-chain) has a tempo divided into [slots](staking-glossary.md#slot) (12 seconds) and [epochs](staking-glossary.md#epoch) (32 slots). The first slot in each epoch is a checkpoint. When a supermajority of validators [attests](staking-glossary.md#attestation) to the link between two checkpoints, they can be [justified](staking-glossary.md#justification) and then when another checkpoint is justified on top, they can be [finalized](staking-glossary.md#finalization).

## Committees

A group of at least 128 [validators](staking-glossary.md#validator) assigned to validate blocks in each [slot](staking-glossary.md#slot). One of the validators in the committee is the aggregator, responsible for aggregating the signatures of all other validators in the committee that agree on an attestation. Not to be confused with [sync committees](staking-glossary.md#sync-committee).

## Consensus layer

TODO

## Deposit contract

The Deposit contract is the **gateway** to Ethereum [Proof of Stake (PoS)](staking-glossary.md#proof-of-stake-pos) and is managed **through a smart contract** on Ethereum. The smart contract accepts any transaction with a minimum amount of 1 ETH and a valid [input data](staking-glossary.md#input-data). Ethereum beacon-nodes listen to the deposit contract and use the input data to credit each validator.

[_More info on the deposit contract_](getting-started/deposit-process.md)

## Effectiveness

The average time it takes for a validator's attestations to be included in the chain.

[_Check out our page explaining validator effectiveness in more detail_](validator-clients/validator-effectiveness.md)

## Epoch

**1 Epoch = 32** [**Slots**](staking-glossary.md#slot)\
Represents the number of 32 slots (12 seconds) and takes approximately **6.4 minutes.** Epochs play an important role when it comes to the [validator queue](staking-glossary.md#validator-queue) and [finality](staking-glossary.md#finalization).

## Execution layer

TODO

## Finalization

In Ethereum [Proof of Stake (PoS)](staking-glossary.md#proof-of-stake-pos) at least two third of the validators have to be honest, therefore if there are two competing [epochs](staking-glossary.md#epoch) and one third of the [validators](staking-glossary.md#validator) decide to be malicious, they will receive a penalty. Honest validators will be rewarded.

In order to determine if an epoch has been finalized, validators have to agree on the latest two epochs in a row, then all previous Epochs can be considered as finalized.

### Finality issues

If there are less than 66.6% of the total possible votes (the [participation rate](staking-glossary.md#participation-rate)) in a specific epoch, the epoch cannot be [justified](staking-glossary.md#justification). As mentioned in "[Finalization](staking-glossary.md#finalization)", three justified epochs in a row are required to reach finality. As long as the chain cannot reach this state it has finality issues.

During finality issues, the validator [entry queue](staking-glossary.md#validator-queue) will be halted and new validators will not be able to join the network, however, inactive validators with less than 16 ETH balance will be exited from the network. This leads to more stability in the network and a higher participation rate, allowing the chain to eventually finalize.

## Fork

A change in protocol causing the creation of an alternative chain or a temporal divergence into two potential block paths.

## Genesis block

The first block in a blockchain, used to initialize a particular network and its cryptocurrency.

## Head vote

The validator has made a timely vote for the correct [head block](staking-glossary.md#chain-head).

## Inactivity leak

If the Beacon Chain has gone more than four [epochs](staking-glossary.md#epoch) without [finalizing](staking-glossary.md#finalization), an emergency protocol called the "inactivity leak" is activated. The ultimate aim of the inactivity leak is to create the conditions required for the chain to recover finality. Finality requires a 2/3 majority of the total staked ether to agree on source and target checkpoints. If validators representing more than 1/3 of the total validators go offline or fail to submit correct [attestations](staking-glossary.md#attestation) then it is not possible for a 2/3 supermajority to finalize checkpoints. The inactivity leak lets the stake belonging to the inactive validators gradually bleed away until they control less than 1/3 of the total stake, allowing the remaining active validators finalize the chain. However large the pool of inactive validators, the remaining active validators will eventually control >2/3 of the stake. The loss of stake is a strong incentive for inactive validators to reactivate as soon as possible!

## Inclusion distance

The inclusion distance of a [slot](staking-glossary.md#slot) is the difference between the slot in which an [attestation](staking-glossary.md#attestation) is made and the lowest slot number of the block in which the attestation is included. For example, an attestation made in slot _s_ and included in the block at slot _s + 1_ has an inclusion distance of _1_. If instead the attestation was included in the block at slot _s + 5_ the inclusion distance would be _5_.

The value of an attestation to the Ethereum network is dependent on its inclusion distance, with a low inclusion distance being preferable. This is because the sooner the information is presented to the network, the more useful it is.

To reflect the relative value of an attestation, the reward given to a validator for attesting is scaled according to the inclusion distance. Specifically, the reward is multiplied by _1/d_, where _d_ is the inclusion distance.

<figure><img src=".gitbook/assets/InclusionDistance.png" alt="Attestation Reward Inclusion Distance Distribution"><figcaption></figcaption></figure>

## Input data

The Input data, also called the **deposit data**, is a user generated, 842 long sequence of characters. It represents the [validator public key](validator-keys.md) and the [withdrawal public key](validator-keys.md), which were signed with by the validator private key. The input data needs to be added to the transaction to the [deposit contract](getting-started/deposit-process.md) in order to get identified by the [beacon-chain](staking-glossary.md#beacon-chain).

[_More info about the deposit process ↗_](getting-started/deposit-process.md)

## Justification

66.6% of the total validators need to attest in favour of a block's inclusion in the [canonical chain](staking-glossary.md#canonical-chain). This condition upgrades the block to "justified". Justified blocks are unlikely to be reverted, but they can be under certain conditions.

When another block is justified on top of a justified block, it is upgraded to "finalized". Finalizing a block is a commitment to include the block in the canonical chain.

[_More info on justification ↗_](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/gasper/)

## Light clients

An Ethereum client that does not store a local copy of the blockchain, or validate blocks and transactions. It offers the functions of a wallet and can create and broadcast transactions.

## MEV

MEV or "maximal extractable value", is a controversial topic. Node operators can extract MEV by accepting blocks built by "searchers", via a small side program called "[mev-boost ↗](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture/11177)" by Flashbots. In this case, the Consensus Layer client such as Nimbus, Teku, etc. will, when asked to procure a block to propose, get blocks from MEV relays via mev-boost and from the Execution Layer client such as Besu, Geth, etc. and then choose whichever block from the relay pays best. The Execution Layer does not currently communicate its expected payout and would only be chosen when the relay offers no block. For this, the relay has to be trusted to deliver valid blocks.

Rewards from MEV are paid to the same [suggested fee recipient](staking-glossary.md#suggested-fee-recipient) address as [priority fees](staking-glossary.md#priority-fees).

[_**Source ↗**_](https://ethereum.org/en/developers/docs/mev/)

## Mempool

TODO

## Participation rate

TODO

## Peers

Other nodes running Ethereum clients that connect to each other over a peer-to-peer network. Communication between peers is how the Ethereum network remains decentralized as there is no single point of failure.

## Priority fees

Almost all transaction on Ethereum set a [priority fee ↗](https://ethereum.org/en/developers/docs/gas/#priority-fee) to incentivize [block proposers](staking-glossary.md#block-proposer) to include the transaction as a higher priority than others. The higher the fee relative to other transactions currently waiting in the [mempool](staking-glossary.md#mempool) This fee is paid to the block proposer. All of the priority fees in a block are aggregated and paid in a single state change directly to the [suggested fee recipient](staking-glossary.md#suggested-fee-recipient) set by the block proposer. This address could be a hardware wallet, a software wallet, or even a multi-sig contract.

## Proof of stake (PoS)

A method by which a cryptocurrency blockchain protocol aims to achieve distributed consensus. PoS asks users to prove ownership of a certain amount of cryptocurrency (their "stake" in the network) in order to be able to participate in the validation of transactions.

## Signing

Demonstrating cryptographically that a message or transaction was approved by the holder of a specific private key.

## Slashable offenses

If your validator commits a slashable offense it will be force exited from the validator pool and will have ETH deducted depending on the circumstances of the event. Typically, this will be 1-2 ETH but could be [significantly more ↗](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/rewards-and-penalties/#slashing).

This is not something to be overly worried about, there are [simple steps](help/slashing-explained.md) you can take to make sure that you don't invoke a slashing event.

There are three ways a validator can be slashed, all of which amount to the dishonest proposal or attestation of blocks.

### Attestation violation

* **Double voting**: Signing two different [attestations](staking-glossary.md#attestation) in one [epoch](staking-glossary.md#epoch).
* **Surround votes**: Attesting to a block that "surrounds" another one (effectively changing history).

### Proposer violation

* **Double block proposal**: [Proposing](staking-glossary.md#block-proposer) and [signing](staking-glossary.md#signing) two different [blocks](staking-glossary.md#block) for the same [slot](staking-glossary.md#slot).

## Slasher node

The [**slasher**](slasher.md) **is its own entity** but requires a beacon-node to receive [attestations](https://kb.beaconcha.in/glossary#attestation). To find malicious activity by validators, the slashers iterates through all received attestations until a **slashable offense** has been found. Found slashings are broadcasted to the network and the next [block proposer](staking-glossary.md#block-proposer) adds the proof to the block. The block proposer receives a reward for slashing the malicious validator. However, the whistleblower (Slasher) does not receive a reward.

## Slot

**32 Slots = 1** [**Epoch**](staking-glossary.md#epoch)\
A time period of **12 seconds** in which a randomly chosen validator has time to propose a block. The total number of validators is split up in [committees](staking-glossary.md#committees) and one or more individual committees are responsible to attest to each slot. One validator from the committee will be chosen to be the aggregator, while the other 127 validators are attesting. After each Epoch, the validators are mixed and merged to new committees. Each slot may or may not have a block in it as a validatory could miss their proposal (e.g. they may be offline or submit their block too late). There is a minimum of 128 validators per committee.

## Source vote

The validator has made a timely vote for the correct source [checkpoint](staking-glossary.md#checkpoint).

## Staking deposit CLI

TODO

## Suggested fee recipient

TODO

## Sync committee

A sync committee is a randomly selected group of [validators](staking-glossary.md#validator) that refresh every \~27 hours. Their purpose is to add their [signatures](staking-glossary.md#signing) to valid block headers. Sync committees allow [light clients](staking-glossary.md#light-clients) to keep track of the head of the blockchain without needing to access the entire validator set. Occurs every 2 years on average, however, there can be "dry spells" multiple times longer than the average without being given one. So if your validator is selected... congratulations! 🥳

## Target vote

The validator has made a timely vote for the correct target [checkpoint](staking-glossary.md#checkpoints).

## Validator

A node in a [Proof of Stake (Pos)](staking-glossary.md#proof-of-stake-pos) system responsible for storing data, processing transactions, and adding new blocks to the blockchain. To activate validator software, you need to be able to stake 32 ETH. A validators job is to propose blocks and sign attestations. It has to be online for at least 50% of the time in order to have positive returns.

### Eligible for activation & Estimated activation

Refers to pending validators. The deposit has been recognized by the [Beacon Chain](staking-glossary.md#beacon-chain) at the timestamp of “Eligible for activation”. If there is a queue of [pending validators ↗](https://www.beaconcha.in/validators), an estimated timestamp for activation is calculated.

### Unique index

Every validator receives a unique index based on when they are added from the [validator queue](staking-glossary.md#validator-queue).

### Current balance & Effective balance

The current balance is the amount of ETH held by the validator as of now. The **effective Balance** represents a value calculated by the current balance. It is used to determine the size of a reward or penalty a validator receives. The effective balance can never be higher than 32 ETH.

In order to increase the effective balance, the validator requires “effective balance + 1.25 ETH”. In other words, if the effective balance is 20 ETH, a current balance of 21.25 ETH is required in order to have an effective balance of 21 ETH. The effective balance will adjust once it drops by 0.25 below the threshold as seen in the examples above.

Here are examples on how the effective balance changes:

* If the Current balance is 32.00 ETH – the Effective balance is 32.00 ETH.
* If the Current balance dropped from 22 ETH to 21.76 ETH – Effective balance will be **22.00 ETH**.
* If the Current balance increases to 22.25 **and** the effective balance is 21 ETH, the effective balance will increase to 22 ETH.

## Validator lifecycle

#### 1. Deposited

32 ETH has been deposited to the ETH1 deposit-contract and this state will be kept for around 7 hours. This offers security in case the Ethereum chain gets attacked.

#### 2. Pending

Waiting for activation on the [Beacon Chain](staking-glossary.md#beacon-chain).

Before validators enter the [validator queue](staking-glossary.md#validator-queue), they need to be voted in by other active validators. This occurs every 4 hours.

#### 3. Active validator

Currently attesting and proposing blocks.

The validator will stay active until:

* Its balance drops below 16 ETH (ejected).
* Voluntary exit.
* It gets slashed.

#### 4. Slashing validator

The Validator has been malicious and will be slashed and kicked out of the system.

> A _**Penalty**_ is a negative reward (e.g. for going offline).\
> A _**Slashing**_ is a large penalty (≥ 1/32 of balance at stake) and a forceful exit ... **. - Justin Drake**

#### 5. Exiting validator

* **Ejected**: The validator balance fell below a threshold and was kicked out by the network.
* **Exited**: Voluntary exit, the withdrawal key holder has the ability to **withdraw** the current balance of the corresponding validator balance.

## Validator pool

The number of currently active validators securing the Ethereum network. The current validator pool can be seen [here ↗](https://beaconcha.in/validators).

## Validator queue

The validator queue is a first-in-first-out queue for activating and exiting validators on the [Beacon Chain](staking-glossary.md#beacon-chain).

* Until 327680 active validators in the network, 4 validators can be activated per [epoch](staking-glossary.md#epoch). For every (4 \* 16384) = **65536** active validators, the validator **activation rate** goes up by one.
* 5 validators per epoch requires 327680 active validators, allowing 1125 validators per day.
* 6 validators per epoch requires 393216 active validators, allowing 1350 validators per day.
* 7 validators per epoch requires 458752 active validators, allowing 1575 validators per day.
* 8 validators per epoch requires 524288 active validators, allowing 1800 validators per day.
* 9 validators per epoch requires 589824 active validators, allowing 2025 validators per day.
* 10 validators per epoch requires 655360 active validators, allowing 2200 validators per day.
* Amount of activations scales with the amount of active validators and the limit is the active validator set divided by 64.

Exiting validators works in the same way, with the amount of validators that can exit the Beacon Chain per day rate limited to preserve the stability of the network.

## Validator seed phrase / mnemonic

The Seed Phrase or Mnemonic is a set of words (usually 12, 18 or 24 words long) used to generate your validator keys. Your mnemonic is the backup for your validator keys and will be the ONLY way to withdraw your ETH when the time comes and no one can help you recover your mnemonic if you lose it.

## Withdrawal address

An address that can be optionally set when creating a validator key that will be used to withdraw staked ETH. If this address is not set at the time of key creation it can be set at the time of withdrawal instead. For more information about setting the withdrawal address on key creation, [see our FAQ answer](faq.md#should-i-set-a-withdrawal-address-when-setting-up-my-solo-staking-validator).