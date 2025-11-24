---
title: チェーン分割リスク (Chain Split Risks)
id: SCWE-033
alias: chain-split-risks
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
チェーン分割リスクは、ハードフォークなどにより、ブロックチェーンが複数のチェーンに分割される際に発生する脆弱性を指します。これは以下につながる可能性があります。
- コントラクトロジックの混乱や不整合。
- 資産やデータの喪失。
- クロスチェーン操作での脆弱性の悪用。

## 対策
- **チェーン分割を対処する:** 潜在的なチェーン分割を対処するロジックを実装します。
- **チェーン識別子を使用する:** チェーン間通信にチェーン識別子を含めます。
- **徹底的にテストする:** 広範なテストを実施して、コントラクトロジックが堅牢であることを確保します。

## 事例
- **チェーン分割に脆弱**
    ```solidity
    pragma solidity ^0.8.0;

    contract ChainSplitVulnerable {
        function processTransaction(bytes memory data) public {
            // Process transaction without chain split handling
        }
    }
    ```

- **チェーン分割に対して保護済み**
    ```solidity
    pragma solidity ^0.8.0;

    contract ChainSplitProtected {
        uint public chainId;

        constructor(uint _chainId) {
            chainId = _chainId;
        }

        function processTransaction(bytes memory data, uint transactionChainId) public {
            require(transactionChainId == chainId, "Invalid chain ID");
            // Process transaction
        }
    }
    ```

---
