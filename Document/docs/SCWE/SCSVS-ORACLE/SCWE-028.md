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
- **Vulnerable to Oracle Manipulation**- Exploiting Weak Price Feeds

- Flash Loan-Based Price Manipulation

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
            payable(msg.sender).transfer(amount / priceOracle.getPrice()); // Uses current price
        }
    }
    ```

Why is this vulnerable?

- Flash loan attack: The attacker can manipulate the price before calling withdraw(), draining funds.
- No validation mechanism to reject manipulated prices.

- **Protected Against Oracle Manipulation**- Fixed Code: Using TWAP & Price Guards

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
            balances[msg.sender] += msg.value * lastValidPrice;
        }

        function withdraw(uint amount) external {
            require(balances[msg.sender] >= amount, "Insufficient balance");
            balances[msg.sender] -= amount;
            payable(msg.sender).transfer(amount / lastValidPrice);
        }
    }
    ```

Fixes:
- Uses TWAP (Time-Weighted Average Price) instead of relying on instantaneous price updates.
- Implements price guards to prevent extreme price fluctuations.
---
