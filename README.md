# StacksOpenScholar - Academic Research Funding Smart Contract

This Clarity smart contract enables a decentralized, transparent system for funding academic research proposals on the **Stacks blockchain**. Researchers can submit proposals, donors can fund them, reviewers can assess their quality, and an admin can release escrowed funds based on community evaluations.

---

## Features

- **Proposal Submission** with milestone tracking and deadlines
- **Reputation-Based Access** to prevent spam and ensure credibility
- **Decentralized Reviews** with comment and rating
- **Smart Fund Escrow & Release** system
- **Admin Approval Workflow**
- **Event Emission** for key state changes

---

## Installation & Deployment

1. Clone this repo or copy the smart contract code.
2. Ensure you have a development environment like [Clarinet](https://docs.stacks.co/clarity/clarinet-cli) installed.
3. Initialize a new project:
   ```bash
   clarinet new research-funding
   cd research-funding
   ```
4. Replace the contents of `contracts/` with your `.clar` file.
5. Deploy locally for testing:
   ```bash
   clarinet check
   clarinet test
   ```

---

##  Smart Contract Interface

### Public Functions

#### 1. `submit-proposal(title, description, requested-amount, milestones, deadline)`
Submit a new research proposal with milestone tracking and deadline.

#### 2. `approve-proposal(proposal-id)`
Approve a pending proposal (admin-only).

#### 3. `fund-proposal(proposal-id, amount)`
Fund an approved proposal. STX will be added to the proposal’s escrow.

#### 4. `submit-review(proposal-id, rating, comment)`
Review a proposal post-submission. Each reviewer can review once (1–5 rating scale).

#### 5. `release-funds(proposal-id)`
Release funds to researcher if the proposal has ≥ 3 reviews and an average rating ≥ 4. (admin-only)

#### 6. `set-min-reputation(new-min-reputation)`
Set the minimum reputation required to submit a proposal. (admin-only)

---

## Data Structures

### Maps

- `Proposals` – Stores proposal metadata and funding status
- `ResearcherBalance` – STX balances for researchers
- `ResearcherReputation` – Tracks researcher credibility
- `Reviews` – Stores per-user reviews per proposal
- `Votes` – Reserved for future governance extensions
- `ActiveResearcherProposals` – Prevents multiple submissions
- `Events` – Stores contract events (proposal submitted, funded, etc.)

###  Variables

- `proposal-count` – Tracks total proposals
- `total-funds` – Total STX allocated to proposals
- `min-reputation-for-proposal` – Threshold for eligibility
- `last-event-id` – For tracking emitted events

---

##  Errors

| Code | Description |
|------|-------------|
| 100  | Not authorized |
| 101  | Invalid amount |
| 102  | Proposal not found |
| 103  | Insufficient funds |
| 104  | Invalid status |
| 105  | Deadline passed |
| ...  | *(see full contract for details)* |

---

## Example Usage

```lisp
;; Submit a new proposal
(contract-call? .research-funding submit-proposal 
  "Decentralized AI Study"
  u"Study on decentralized AI research systems."
  u1000000
  (list "Phase 1: Literature Review" "Phase 2: Experiments")
  (+ (block-height) u100))

;; Approve proposal (admin)
(contract-call? .research-funding approve-proposal u1)

;; Fund proposal
(contract-call? .research-funding fund-proposal u1 u500000)

;; Submit review
(contract-call? .research-funding submit-review u1 u5 u"Excellent potential")

;; Release funds (admin)
(contract-call? .research-funding release-funds u1)
```

---