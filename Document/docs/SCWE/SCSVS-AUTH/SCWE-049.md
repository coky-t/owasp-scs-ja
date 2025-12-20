---
title: 保護されていない Ether の引き落とし (Unprotected Ether Withdrawal)
id: SCWE-049
alias: unprotected-ether-withdrawal
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [732]
status: new
---

## 関連
- CWE-732: Incorrect Permission Assignment for Critical Resource  
  [https://cwe.mitre.org/data/definitions/732.html](https://cwe.mitre.org/data/definitions/732.html)

## 説明
保護されていない Ether の引き落としはスマートコントラクトが十分なアクセス制御や適切な認可メカニズムなしで Ether (ETH) の引き落としを許可した場合に発生します。この脆弱性はコントラクトを不正なユーザーや悪意のあるユーザーにさらし、引き落としリクエストをいかなるチェックもなしで資金を引き出すことを可能にします。

## 対策
To prevent this vulnerability, implement strong access control mechanisms like the `onlyOwner` modifier or a more flexible role-based access control (RBAC) system. Ensure that only authorized users (e.g., the contract owner or specific users) can initiate Ether withdrawals. Always verify user permissions before allowing access to critical contract functions.

## 影響
An attacker can exploit this vulnerability to withdraw Ether from the contract, leading to potential financial loss. This could result in a total loss of funds if no protective measures are in place, exposing the contract and its users to significant risks.

### 脆弱なコントラクトの例

```solidity
contract EtherWallet {
    address public owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    function withdraw(uint amount) public {
        payable(msg.sender).transfer(amount);  // No access control
    }
}
```

### 修正したコントラクトの例

```solidity
contract EtherWallet {
    address public owner;
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }
    
    constructor() {
        owner = msg.sender;
    }
    
    function withdraw(uint amount) public onlyOwner {
        payable(msg.sender).transfer(amount);  // Access control added
    }
}
```
