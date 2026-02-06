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
この脆弱性を防ぐには、`onlyOwner` 修飾子や、より柔軟なロールベースのアクセス制御 (RBAC) システムなどの強力なアクセス制御メカニズムを実装します。認可されたユーザー (コントラクト所有者や特定のユーザーなど) のみが Ether の引き落としを開始できるように確保します。重要なコントラクト機能へのアクセスを許可する前に、必ずユーザーパーミッションを検証します。

## 影響
攻撃者はこの脆弱性を悪用してコントラクトから Ether を引き落とし、潜在的な金銭的損失につながる可能性があります。保護対策を講じていない場合、これは資金の全額損失につながり、コントラクトとそのユーザーを重大なリスクにさらします。

### 脆弱なコントラクトの例

```solidity
contract EtherWallet {
    address public owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    function withdraw(uint amount) public {
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "Transfer failed");  // No access control
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
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "Transfer failed");  // Access control added
    }
}
```
