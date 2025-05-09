# MultiSigWallet

A **Multi-Signature Wallet** (`MultiSigWallet`) implemented in Solidity for Ethereum-compatible blockchains.  
This contract allows multiple owners to manage and approve transactions before execution, providing enhanced security for fund management.

---

## Features

- **Multiple Owners:** Wallet is managed by a group of owners.
- **Approval Threshold:** A transaction must collect a minimum number of owner approvals before execution.
- **Transaction Lifecycle:** Owners can **submit**, **approve**, **execute**, and **revoke** transactions.
- **Event Emission:** Important events like deposits, submissions, approvals, revocations, and executions are logged.

---

## Events

- `Deposit(address indexed sender, uint amount)`
- `Submit(uint indexed txId)`
- `Approve(address indexed owner, uint indexed txId)`
- `Revoke(address indexed owner, uint indexed txId)`
- `Execute(uint indexed txId)`

---

## Contract Details

### Structs
- `Transaction`
  - `address to`
  - `uint value`
  - `bytes data`
  - `bool executed`

### State Variables
- `address[] public owners` — List of wallet owners.
- `mapping(address => bool) public isOwner` — Tracks if an address is an owner.
- `uint public required` — Number of approvals required to execute a transaction.
- `Transaction[] public transactions` — List of all submitted transactions.
- `mapping(uint => mapping(address => bool)) public approved` — Tracks approvals for each transaction.

---

## Constructor

```solidity
constructor(address[] memory _owners, uint _required)
```
- Initializes the contract with a list of owners and the number of required approvals.
- Validates:
  - At least one owner is provided.
  - `required` is greater than 0 and does not exceed the number of owners.
  - All owners are unique and not address(0).

---

## Functions

### Receive Ether

```solidity
receive() external payable
```
- Accepts incoming Ether deposits and emits the `Deposit` event.

---

### Submit a Transaction

```solidity
function submit(address _to, uint _value, bytes calldata _data) external onlyOwner
```
- Propose a new transaction to be approved by owners.

---

### Approve a Transaction

```solidity
function approve(uint _txId) external onlyOwner txExists(_txId) notApproved(_txId) notExecuted(_txId)
```
- Approve a pending transaction.

---

### Execute a Transaction

```solidity
function execute(uint _txId) external txExists(_txId) notExecuted(_txId)
```
- Execute a transaction once it has enough approvals.
- Funds are transferred via a low-level call.

---

### Revoke Approval

```solidity
function revoke(uint _txId) external onlyOwner txExists(_txId) notExecuted(_txId)
```
- Revoke a previously given approval for a transaction.

---

### View Functions

- `getOwners()` → Returns all owners.
- `getTransactionCount()` → Returns the total number of transactions.
- `getTransaction(uint _txId)` → Returns the transaction details (to, value, data, executed, approvals).

---

## Security Considerations

- **Transaction Replay Protection:** Each transaction can only be executed once.
- **Owner Verification:** Only owners can submit, approve, revoke, or execute.
- **Minimum Approval Enforcement:** Transactions can only be executed after collecting the required number of approvals.

---

## Example Usage

1. Deploy the contract with a list of owners and a required number of approvals.
2. Any owner submits a transaction.
3. Other owners approve the transaction.
4. Once the number of approvals meets the requirement, any owner can execute it.
5. Owners can also revoke their approval before execution if needed.

---


