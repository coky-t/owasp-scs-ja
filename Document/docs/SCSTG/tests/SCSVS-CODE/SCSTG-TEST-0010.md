---
id: SCSTG-TEST-0010
scsvs_cg_id:
- SCSVS-CODE
scsvs_scg_id:
- SCSVS-CODE-2
platform: []
title: コンパイラバージョンと非推奨関数のテスト (Test Compiler Version and Deprecated Functions)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0010
---

### **説明**
開発ポリシー、セキュアコーディングスタンダード、コードの明確さ、テストカバレッジを適切に管理することで、スマートコントラクトの安全性、保守性、脆弱性に対する耐性を確保します。このテストはスマートコントラクトの開発とレビューに関するベストプラクティスとガイドラインの遵守を確保することに重点を置いています。これには、最新のコンパイラの使用、非推奨関数の回避、徹底したコードレビュー、適切なテストカバレッジを含みます。コードの明確さは、コントラクトを長期にわたって維持し、理解しやすく監査可能な状態を維持するためにも重要です。

---

### **テスト 1: コンパイラバージョンを検証して非推奨関数を回避する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.4.0;

contract Example {
    uint256 public value;

    function setValue(uint256 _value) public {
        value = _value;
    }
}
```
#### **なぜ脆弱なのか**
- コントラクトは古いコンパイラバージョン (^0.4.0) を使用しています。古いバージョンの Solidity を使用すると、既知のセキュリティ脆弱性や最新機能のサポートの欠如につながる可能性があります。
- また、古いコンパイラは新しいバージョンで利用できる最適化やセキュリティ修正も欠いています。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;  // Update to the latest stable version

contract Example {
    uint256 public value;

    function setValue(uint256 _value) public {
        value = _value;
    }
}
```
#### **チェック方法**
- コードレビュー: pragma ディレクティブで最新バージョンの Solidity (例: ^0.8.x) を指定しており、^0.4.x などの古いものではないことを確認します。
- 自動チェック: Solidity リンティングツールまたは自動 CI/CD パイプラインを使用して、古いコンパイラバージョンの使用をフラグ付けします。

---

### **テスト 2: コードレビュープロセスを確認して非推奨関数を回避する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.4.24;

contract Token {
    string public name = "Token";
    uint public totalSupply = 1000000;

    // Deprecated function in Solidity 0.4.x
    function transfer(address recipient, uint amount) public {
        require(msg.sender != recipient, "Cannot transfer to yourself");
        require(amount <= totalSupply, "Amount exceeds total supply");

        totalSupply -= amount;
        // Deprecated: The transfer method below is obsolete
        recipient.transfer(amount);
    }
}

```

#### **なぜ脆弱なのか**
- 上記の例の関数 transfer は非推奨の `transfer()` メソッドを使用して、受信者に Ether を送信します。
- `transfer()` メソッドは Solidity 0.5.x で削除されました。新しい Solidity バージョンでのガス制限と転送動作の変更によるエラーを防ぐため、代わりに `call()` を使用することをお勧めします。
- 非推奨関数を使用すると、コントラクトは Solidity の将来のバージョンと互換性がなくなり、予期しない動作に脆弱なままとなる可能性があります。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract Token {
    string public name = "Token";
    uint public totalSupply = 1000000;

    function transfer(address payable recipient, uint amount) public {
        require(msg.sender != recipient, "Cannot transfer to yourself");
        require(amount <= totalSupply, "Amount exceeds total supply");

        totalSupply -= amount;
        // Replaced deprecated transfer method with call to ensure proper handling
        (bool success, ) = recipient.call{value: amount}("");
        require(success, "Transfer failed");
    }
}

```

#### **なぜ修正が機能するのか**
- 更新されたコードは Ether を送信するために、非推奨の `transfer()` の代わりに `call() メソッドを使用します。
- `call()` メソッドはより柔軟性が高く、ガスを指定してエラーを適切に処理できるため、Solidity 0.5.x 以上で推奨されます。
- この変更は、コントラクトが新しい Solidity バージョン (>=0.5.x) と互換があることを確保し、将来のアップグレードでの潜在的な問題を回避します。

#### **チェック方法**
- コードレビュー: コントラクトが非推奨の関数や廃止された関数を使用していないことを確認します。Ether 送信時の、`transfer()`、`send()` 、その他の古いメソッドを探し、より安全な `call()` メソッドで置き換えます。
- 静的解析ツール: SolidityScan, MythX, Slither, リンターなどのツールを使用して、非推奨の機能を検出し、コードを更新するための提案を提供します。
- テスト: 最新の Solidity バージョンを使用してコントラクトをテストし、非推奨の関数が使用されていないことを検証して、新しいコンパイラとの互換性を確保します。
