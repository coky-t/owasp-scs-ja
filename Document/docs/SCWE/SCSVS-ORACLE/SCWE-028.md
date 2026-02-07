---
title: 価格オラクル操作 (Price Oracle Manipulation)
id: SCWE-028
alias: price-oracle-manipulation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
価格オラクル操作は価格オラクルの脆弱性を悪用してコントラクトロジックを操作することを指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **分散型オラクルを使用する:** 価格データに複数の分散型オラクルを活用します。
- **入力を検証する:** 使用前にすべてのオラクルデータが適切に検証されていることを確保します。
- **サーキットブレーカーを実装する:** 疑わしいアクティビティの場合に操作を停止するメカニズムを追加します。

## 事例
- **オラクル操作の脆弱性**- 脆弱な価格フィードの悪用

- フラッシュローンベースの価格操作

    ```solidity
    pragma solidity ^0.8.0;

    interface Oracle {
        function getPrice() external view returns (uint);
    }

    contract VulnerableLending {
        Oracle public priceOracle;
        mapping(address => uint) public balances;

        constructor(address _oracle) {
            priceOracle = Oracle(_oracle);
        }

        function deposit() external payable {
            balances[msg.sender] += msg.value * priceOracle.getPrice();
        }

        function withdraw(uint amount) external {
            require(balances[msg.sender] >= amount, "Insufficient balance");
            balances[msg.sender] -= amount;
            (bool ok, ) = msg.sender.call{value: amount / priceOracle.getPrice()}(""); // Uses current price
            require(ok, "Transfer failed");
        }
    }
    ```

なぜこれが脆弱なのか？

- フラッシュローン攻撃: 攻撃者は withdraw() を呼び出す前に価格を操作し、資金を流出する可能性があります。
- 操作された価格を拒否するバリデーションメカニズムがありません。

- **オラクル操作に対する保護**- 修正されたコード: 価格逸脱ガードの使用

    ```solidity
    pragma solidity ^0.8.0;

    interface Oracle {
        function getPrice() external view returns (uint);
    }

    contract SecureLending {
        Oracle public priceOracle;
        mapping(address => uint) public balances;
        uint public lastValidPrice;

        constructor(address _oracle) {
            priceOracle = Oracle(_oracle);
            lastValidPrice = priceOracle.getPrice();
        }

        function updatePrice() external {
            uint newPrice = priceOracle.getPrice();
            require(newPrice > lastValidPrice * 95 / 100 && newPrice < lastValidPrice * 105 / 100, "Price deviation too high");
            lastValidPrice = newPrice;
        }

        function deposit() external payable {
            updatePrice(); // refresh price before use
            balances[msg.sender] += msg.value * lastValidPrice;
        }

        function withdraw(uint amount) external {
            updatePrice(); // refresh price before use
            require(balances[msg.sender] >= amount, "Insufficient balance");
            balances[msg.sender] -= amount;
            (bool ok, ) = msg.sender.call{value: amount / lastValidPrice}("");
            require(ok, "Transfer failed");
        }
    }
    ```

修正内容:
- 価格逸脱ガード (最大 5% の変化) を実装し、操作された価格や古い価格を拒否します。
- 入金/出金の前に `updatePrice()` を呼び出し、`lastValidPrice` が最新であることを確保します。
---
