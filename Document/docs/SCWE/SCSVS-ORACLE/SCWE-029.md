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
- **Use multiple oracles:** Leverage multiple decentralized oracles for critical data.
- **Validate inputs:** Ensure all oracle data is properly validated before use.
- **Implement fallback mechanisms:** Use fallback oracles in case of failure.

## 事例
- **Single Oracle Source/ Single Point of Failure**
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

Why is this vulnerable?
- If the oracle fails, is compromised, or is manipulated, the contract has no fallback.
- Attackers could hijack the single oracle and return malicious data.

- **Decentralized Oracle Sources- Using Multiple Oracles & Fallbacks**
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
Fixes:
- Uses multiple oracles and computes an average to prevent manipulation.
- If one oracle fails or gets compromised, the contract still functions correctly.

--
