---
title: 任意のストレージ位置への書き込み (Write to Arbitrary Storage Location)
id: SCWE-057
alias: write-arbitrary-storage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html)


## 説明
Writing to arbitrary storage locations can occur when a contract fails to properly validate inputs before interacting with storage variables. An attacker may exploit this vulnerability to overwrite sensitive storage slots, leading to unintended contract behavior, state manipulation, or loss of funds.

## 対策
To mitigate this vulnerability, ensure that all inputs to storage-related operations are properly validated. Avoid allowing external users to specify arbitrary storage locations. Use access control mechanisms to restrict who can modify sensitive state variables and ensure that only trusted users or contract functions can write to critical storage locations.

### 脆弱なコントラクトの例
```solidity
contract ArbitraryStorageWrite {
    uint256 public balance;

    function setStorage(uint256 storageLocation, uint256 value) public {
        assembly {
            sstore(storageLocation, value)  // Writing to arbitrary storage location
        }
    }
}
```

### 修正したコントラクトの例
```solidity
contract SecureStorageWrite {
    uint256 public balance;
    address public owner;

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function setBalance(uint256 value) public onlyOwner {
        balance = value;  // Only the owner can modify the balance
    }
}
```
