---
id: SCSTG-TEST-0001
scsvs_cg_id:
- SCSVS-AUTH
scsvs_scg_id:
- SCSVS-AUTH-1
platform: []
title: マルチ署名スキームのテスト (Testing Multi-Signature Schemes)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0001
---

重要な操作にはマルチ署名スキームが実装されていることを確認し、複数の承認者からの承認を要求することで、セキュリティを強化し、不正なアクションのリスクを軽減します。

- 機密性の高い操作を実行する前に、マルチ署名ロジックが構成可能な承認の閾値を確保していることを検証します。
- すべての署名者が認証されており、重複した署名が拒否されることを確認します。
- 不十分な承認や改竄されたデータなど、エッジケースをテストします。

```solidity
// Example of multi-signature scheme
pragma solidity ^0.8.0;

contract MultiSigWallet {
    address[] public owners;
    uint public requiredApprovals;
    mapping(address => bool) public isOwner;
    mapping(uint => mapping(address => bool)) public approvals;

    struct Transaction {
        address to;
        uint value;
        bool executed;
    }

    Transaction[] public transactions;

    constructor(address[] memory _owners, uint _requiredApprovals) {
        require(_owners.length > 0, "Owners required");
        require(_requiredApprovals > 0 && _requiredApprovals <= _owners.length, "Invalid approval count");

        for (uint i = 0; i < _owners.length; i++) {
            isOwner[_owners[i]] = true;
        }
        owners = _owners;
        requiredApprovals = _requiredApprovals;
    }

    function submitTransaction(address _to, uint _value) public {
        require(isOwner[msg.sender], "Not an owner");
        transactions.push(Transaction({to: _to, value: _value, executed: false}));
    }

    function approveTransaction(uint _txIndex) public {
        require(isOwner[msg.sender], "Not an owner");
        require(!approvals[_txIndex][msg.sender], "Already approved");

        approvals[_txIndex][msg.sender] = true;
    }

    function executeTransaction(uint _txIndex) public {
        require(transactions[_txIndex].executed == false, "Already executed");

        uint approvalCount = 0;
        for (uint i = 0; i < owners.length; i++) {
            if (approvals[_txIndex][owners[i]]) {
                approvalCount++;
            }
        }

        require(approvalCount >= requiredApprovals, "Not enough approvals");

        transactions[_txIndex].executed = true;
        (bool ok, ) = payable(transactions[_txIndex].to).call{value: transactions[_txIndex].value}("");
        require(ok, "Transfer failed");
    }

    receive() external payable {}
}
```
ロジックをレビューして、すべての承認が実行前に検証され、重複する承認が防止され、トランザクションデータの完全性が維持されていることを確認します。さまざまな承認シナリオでテストを行い、エッジケースの適切な処理を検証します。
