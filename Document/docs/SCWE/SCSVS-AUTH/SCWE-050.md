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
To mitigate this vulnerability, it is crucial to protect the `SELFDESTRUCT` instruction with proper access control mechanisms. Only authorized users, such as the contract owner or admin, should be allowed to call the `SELFDESTRUCT` function. Consider using modifiers like `onlyOwner` or a role-based access control system to enforce permission checks before allowing this critical operation.

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
