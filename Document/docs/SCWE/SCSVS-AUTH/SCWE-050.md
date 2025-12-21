---
title: 保護されていない SELFDESTRUCT 命令 (Unprotected SELFDESTRUCT Instruction)
id: SCWE-050
alias: unprotected-selfdestruct
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
Ethereum の `SELFDESTRUCT` 命令は、コントラクトが自身を破壊し、残りの Ether 残高を使用されたアドレスに送信することを可能にします。この命令がアクセス制御で適切に保護されていない場合、攻撃者はそれをトリガーし、コントラクトに自己破壊を引き起こし、すべての資金を不正なアドレスに送金する可能性があります。これは、資金の全額損失やコントラクト機能の中断につながる可能性があります。

## 対策
この脆弱性を緩和するには、適切なアクセス制御メカニズムを用いて `SELFDESTRUCT` 命令を保護することが重要です。コントラクト所有者や管理者など、認可されたユーザーのみが `SELFDESTRUCT` 関数を呼び出すことができるようにする必要があります。この重要な操作を許可する前に `onlyOwner` などの修飾子やロールベースのアクセス制御システムを使用してパーミッションチェックを実施することを検討します。

### 脆弱なコントラクトの例
```solidity
contract Destructible {
    address public owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    // Unprotected SELFDESTRUCT allowing anyone to call it
    function destruct() public {
        selfdestruct(payable(msg.sender));  // No access control
    }
}
```

### 修正したコントラクトの例
```solidity
contract Destructible {
    address public owner;
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }
    
    constructor() {
        owner = msg.sender;
    }
    
    // Only the owner can call SELFDESTRUCT
    function destruct() public onlyOwner {
        selfdestruct(payable(msg.sender));  // Access control added
    }
}
```
