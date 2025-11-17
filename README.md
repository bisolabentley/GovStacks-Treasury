
# GovStacks-Treasury - Treasury Management Contract**

*A Clarity Smart Contract for Proposals, Voting, Member Governance & Treasury Operations*

## **Overview**

This project implements a governance-driven DAO treasury system on the Stacks blockchain. It allows DAO members to create proposals, vote based on weighted membership, and execute approved treasury actions. Additionally, the contract integrates optional staking functionality through a pluggable staking pool trait.

The contract emphasizes **security**, **input validation**, and **role-based authorization**, ensuring the DAO operates transparently and predictably.

---

## **Features**

### **🔐 Governance & Membership**

* **DAO Owner Controls**

  * Add members with weights
  * Update member voting weights
  * Transfer DAO ownership

* **Weighted Membership Voting**

  * Each member is assigned a weight (`1`–`1,000,000`)
  * Prevents unauthorized voting
  * Ensures democratic, weighted voting outcomes

### **📜 Proposal System**

* Members can submit proposals with:

  * Title
  * Description
  * Transfer amount
  * Recipient

* A proposal includes:

  * Yes / No vote tallies
  * Voting duration (24h default = 144 blocks)
  * Prevents duplicate voting
  * Automatic validation for title, description, amount, and recipients

### **✔️ Proposal Execution**

* Executes only if:

  * Proposal is still active
  * Yes votes > No votes
  * Treasury has sufficient funds

* On execution:

  * Contract transfers STX to the designated recipient
  * Marks the proposal as executed

### **💰 Treasury & Staking**

* DAO owner can stake/unstake treasury funds via 3rd-party staking contracts
* Uses a Clarity trait to interact with any compliant staking pool
* Prevents staking more STX than the treasury holds

### **⚠️ Safety Validations**

* Many layers of validation including:

  * Invalid weights
  * Empty titles/descriptions
  * Zero or insufficient STX amounts
  * Expired proposals
  * Unauthorized callers
  * Duplicate votes

---

## **Contract Structure**

### **Data Maps**

* `proposals`: Stores all proposal details
* `votes`: Tracks if a user has already voted on a proposal
* `member-weights`: Stores each member’s voting weight

### **Data Variables**

* `proposal-count`: Auto-incrementing proposal ID counter
* `dao-owner`: Address with admin authority
* `total-members`: Count of registered DAO members

### **Read-Only Functions**

* `get-proposal`
* `get-member-weight`
* `has-voted`

### **Public Functions**

**Governance**

* `add-member`
* `update-member-weight`
* `change-owner`

**Proposals**

* `create-proposal`
* `vote`
* `execute-proposal`

**Staking**

* `stake-treasury-funds`
* `unstake-treasury-funds`

---

## **Error Codes**

The contract defines clear error constants for traceability:

* `ERR-NOT-AUTHORIZED`
* `ERR-PROPOSAL-NOT-FOUND`
* `ERR-INSUFFICIENT-FUNDS`
* `ERR-ALREADY-VOTED`
* `ERR-PROPOSAL-EXPIRED`
* `ERR-INVALID-WEIGHT`
* …and others for titles, descriptions, recipients, and amounts.

---

## **Security Notes**

* Only the DAO owner can add/update members and stake treasury funds
* A proposal cannot be executed twice
* Contract prevents transferring funds to the proposer or DAO owner
* All external calls (staking) use `contract-call?` wrapped with `try!`
* Maximum constraints prevent oversized proposal input strings

---

## **How to Use**

### **1. Deploy Contract**

Deploy the Clarity contract on a Stacks testnet or mainnet environment.

### **2. Initialize DAO Owner**

The deploying principal automatically becomes the DAO owner.

### **3. Add Members**

```clarity
(add-member 'SP123... u100)
```

### **4. Create a Proposal**

```clarity
(create-proposal 
  "Fund Grant"
  "Proposal to fund project X"
  u1500000
  'SP456...
)
```

### **5. Vote on a Proposal**

```clarity
(vote u1 true)   ;; vote yes
```

### **6. Execute Proposal**

```clarity
(execute-proposal u1)
```

### **7. Stake Treasury Funds**

```clarity
(stake-treasury-funds u100000 <pool-contract>)
```

---


