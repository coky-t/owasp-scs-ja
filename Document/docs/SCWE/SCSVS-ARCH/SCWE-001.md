---
title: 不適切なコントラクトアーキテクチャ (Improper Contract Architecture)
id: SCWE-001
alias: improper-contract
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [709]
status: new
---

## 関連
- CWE-1008: Architectural Concepts  
  [https://cwe.mitre.org/data/definitions/1008.html](https://cwe.mitre.org/data/definitions/1008.html)

## 説明
不適切なコントラクトアーキテクチャは、スマートコントラクトの構造設計に欠陥があり、そのセキュリティ、スケーラビリティ、保守性を損なうことを指します。これは、モノリシックな設計、非効率な継承構造、不十分な関心の分離によって発生することがよくあります。そのような欠陥は、コントラクトを管理、アップグレード、監査することを困難にします。不適切なアーキテクチャに関連する主な問題は以下のとおりです。

- リスクを伴わずにコントラクトをアップグレードすることが困難である。
- 複雑さが増し、隠れた攻撃対象領域につながる。
- コントラクトロジックの柔軟性に欠け、バグやエクスプロイトの特定が困難となる。

## 対策
- **Modular design:** Break down contracts into smaller, more manageable modules to ensure a clean separation of concerns.
- **Proxy pattern implementation:** Use proxies to allow contract upgrades while preserving contract state.
- **Separate logic and state:** Keep business logic and data storage separate to improve scalability and maintainability.
- **Periodic code review and refactoring:** Regularly assess the architecture and refactor to maintain efficiency and security.

## 事例

### 不適切なコントラクトアーキテクチャ

```solidity
pragma solidity ^0.8.0;

contract MonolithicContract {
    uint public balance;
    address public owner;

    mapping(address => uint) public allowances;

    constructor() {
        owner = msg.sender;
    }

    function deposit(uint value) public {
        balance += value;
    }

    function withdraw(uint value) public {
        require(balance >= value, "Insufficient funds");
        balance -= value;
        payable(msg.sender).transfer(value);
    }

    function transfer(address to, uint value) public {
        require(balance >= value, "Insufficient funds");
        balance -= value;
        payable(to).transfer(value);
    }

    function approve(address spender, uint value) public {
        allowances[spender] = value;
    }

    function transferFrom(address from, address to, uint value) public {
        require(allowances[from] >= value, "Allowance exceeded");
        allowances[from] -= value;
        payable(to).transfer(value);
    }

    function upgradeLogic() public {
        require(msg.sender == owner, "Not authorized");
        // Upgrading logic is impossible without deploying a new contract.
    }
}
```

**Problem**: The contract has a monolithic design, tightly coupling storage, business logic, and access control. This makes upgrading or fixing specific parts difficult.


### 改良したモジュラーコントラクトアーキテクチャ

```solidity
pragma solidity ^0.4.0;

contract Deposit {
    uint public balance;

    function deposit() public {
        // Logic for deposit
    }
}

contract Withdraw {
    uint public balance;

    function withdraw() public {
        // Logic for withdrawal
    }
}

contract Transfer {
    uint public balance;

    function transfer() public {
        // Logic for transfer
    }
}
```

**Solution**: Separate storage from logic using proxy patterns. Now the contract logic can be updated without touching storage!
