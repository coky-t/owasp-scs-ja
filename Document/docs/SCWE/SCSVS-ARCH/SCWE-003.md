---
title: モジュール性の欠如 (Lack of Modularity)
id: SCWE-003
alias: lack-of-modularity
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [1047]
status: new
---

## 関連
- CWE-1047: Modules with Circular Dependencies  
  [https://cwe.mitre.org/data/definitions/1047.html](https://cwe.mitre.org/data/definitions/1047.html)

## 説明
モジュール性の欠如は、システムのコンポーネントが独立した再使用可能なモジュールに十分に分離されていない、設計上の欠陥を指します。この欠陥はコードの密結合につながり、システムを理解、保守、拡張することが困難になります。スマートコントラクトのコンテキストでは、これはすべての機能がバンドルされたモノリシックなコントラクトとして現れ、複雑さとエラーの可能性を高めます。

## 対策
- **モジュール化設計:** コントラクトを、特定の責任を担う、より小さく焦点を当てたモジュールに分割します。
- **ライブラリの使用:** 既存の十分にテストされたライブラリを活用して共通機能を処理し、カスタムコードの必要性を減らします。
- **ロジックの簡素化:** コントラクトのロジックを合理化し、冗長なコードを削除することで、不要な複雑さを回避します。
- **定期的な監査:** 定期的にコードレビューと監査を実施し、過度に複雑な領域を特定して対処します。

## 事例

### モジュール性の欠如の例:

```solidity
pragma solidity ^0.8.0;

contract TightlyCoupledContract {
    mapping(address => uint) public balances;
    mapping(address => mapping(address => uint)) public allowances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint amount) public {
        require(balances[msg.sender] >= amount, "Insufficient funds");
        balances[msg.sender] -= amount;
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "Transfer failed");
    }

    function transfer(address to, uint amount) public {
        require(balances[msg.sender] >= amount, "Insufficient funds");
        balances[msg.sender] -= amount;
        balances[to] += amount;
    }

    function approve(address spender, uint amount) public {
        allowances[msg.sender][spender] = amount;
    }

    function transferFrom(address from, address to, uint amount) public {
        require(allowances[from][msg.sender] >= amount, "Allowance exceeded");
        allowances[from][msg.sender] -= amount;
        balances[from] -= amount;
        balances[to] += amount;
    }
}
```

**問題点:** 独立した再使用可能なモジュールに関心を分離するのではなく、すべてが一つのコントラクト内で処理されています。

なぜこれがモジュール性の欠如なのか？

- 単一のコントラクトは複数の責任 (残高管理、承認、送金) を処理します。
- コードは再使用可能ではありません。別のコントラクトが残高機能を必要である場合、子のロジックをコピー＆ペーストする必要があります。
- テストが困難です。transfer() を変更すると、withdraw() が機能しなくなる可能性があります。これは密結合しているためです。

### モジュラー設計でリファクタリングしたもの:

```solidity
pragma solidity ^0.8.0;

library BalanceLibrary {
    struct Data {
        mapping(address => uint) balances;
    }

    function deposit(Data storage self, address user, uint amount) internal {
        self.balances[user] += amount;
    }

    function withdraw(Data storage self, address user, uint amount) internal {
        require(self.balances[user] >= amount, "Insufficient funds");
        self.balances[user] -= amount;
        (bool ok, ) = payable(user).call{value: amount}("");
        require(ok, "Transfer failed");
    }
}

library AllowanceLibrary {
    struct Data {
        mapping(address => mapping(address => uint)) allowances;
    }

    function approve(Data storage self, address owner, address spender, uint amount) internal {
        self.allowances[owner][spender] = amount;
    }

    function transferFrom(
        Data storage self,
        BalanceLibrary.Data storage balances,
        address from,
        address to,
        uint amount
    ) internal {
        require(self.allowances[from][msg.sender] >= amount, "Allowance exceeded");
        require(balances.balances[from] >= amount, "Insufficient funds");

        self.allowances[from][msg.sender] -= amount;
        balances.balances[from] -= amount;
        balances.balances[to] += amount;
    }
}

contract ModularContract {
    using BalanceLibrary for BalanceLibrary.Data;
    using AllowanceLibrary for AllowanceLibrary.Data;

    BalanceLibrary.Data private balances;
    AllowanceLibrary.Data private allowances;

    function deposit() public payable {
        balances.deposit(msg.sender, msg.value);
    }

    function withdraw(uint amount) public {
        balances.withdraw(msg.sender, amount);
    }

    function approve(address spender, uint amount) public {
        allowances.approve(msg.sender, spender, amount);
    }

    function transferFrom(address from, address to, uint amount) public {
        allowances.transferFrom(balances, from, to, amount);
    }
}

```

**解決策:** 共有機能にはライブラリを使用してコントラクトの関心を分離します。これで、ストレージが分離され、再使用可能なライブラリが使用され、責任が分割されています！
