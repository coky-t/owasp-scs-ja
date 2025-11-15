---
title: 分散型オラクルソースの欠如 (Lack of Decentralized Oracle Sources)
id: SCWE-029
alias: lack-of-decentralized-oracle-sources
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
分散型オラクルソースの欠如は、重要なデータを単一のオラクルに依存することを指し、操作や侵害される恐れがあります。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **複数のオラクルを使用する:** 重要なデータには複数の分散型オラクルを活用します。
- **入力を検証する:** すべてのオラクルデータが使用前に適切に検証されていることを確保します。
- **フォールバックメカニズムを実装する:** 障害発生時にはフォールバックオラクルを使用します。

## 事例
- **単一のオラクルソース/ 単一障害点**
    ```solidity
    pragma solidity ^0.8.0;

    interface Oracle {
        function getPrice() external view returns (uint);
    }

    contract SingleOracle {
        Oracle public priceOracle;

        constructor(address _oracle) {
            priceOracle = Oracle(_oracle);
        }

        function getPrice() public view returns (uint) {
            return priceOracle.getPrice(); // Single source of truth
        }
    }
    ```

なぜこれが脆弱なのか？
- オラクルに障害が発生したり、侵害されたり、操作された場合、そのコントラクトにはフォールバックがありません。
- 攻撃者は単一のオラクルを乗っ取り、悪意のあるデータを返す可能性があります。

- **分散型オラクルソース - 複数のオラクルとフォールバックの使用**
    ```solidity
    pragma solidity ^0.8.0;

    interface Oracle {
        function getPrice() external view returns (uint);
    }

    contract MultiOracle {
        Oracle[] public priceOracles;

        constructor(address[] memory _oracles) {
            for (uint i = 0; i < _oracles.length; i++) {
                priceOracles.push(Oracle(_oracles[i]));
            }
        }

        function getPrice() public view returns (uint) {
            uint totalPrice = 0;
            for (uint i = 0; i < priceOracles.length; i++) {
                totalPrice += priceOracles[i].getPrice();
            }
            return totalPrice / priceOracles.length; // Averaging multiple oracles
        }
    }
    ```
修正内容:
- 複数のオラクルを使用し、平均値を計算して、不正操作を防ぎます。
- 一つのオラクルが障害発生したり、侵害された場合、そのコントラクトは依然として正常に機能します。

---
