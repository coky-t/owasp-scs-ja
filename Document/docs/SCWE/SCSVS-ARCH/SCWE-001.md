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
- **モジュール化設計:** コントラクトをより小さく管理しやすいモジュールに分割し、明確な関心の分離を確保します。
- **プロキシパターン実装:** プロキシを使用して、コントラクトの状態を維持しながらコントラクトのアップグレードを可能にします。
- **ロジックと状態の分離:** ビジネスロジックとデータストレージを分離し続け、スケーラビリティと保守性を向上します。
- **定期的なコードレビューとリファクタリング:** 定期的にアーキテクチャを評価し、効率性とセキュリティを維持するためにリファクタします。

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

**問題点**: コントラクトはモノリシックな設計で、ストレージ、ビジネスロジック、アクセス制御を密接に結合しています。これは特定の部分をアップグレードしたり修正することが困難になります。


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

**解決策**: プロキシパターンを使用してストレージとロジックを分離します。これでコントラクトロジックはストレージにタッチすることなく更新できます。
