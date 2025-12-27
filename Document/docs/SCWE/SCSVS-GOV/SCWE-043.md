---
title: フォールバック関数の安全でない使用 (Insecure Use of Fallback Functions)
id: SCWE-043
alias: insecure-use-of-fallback-functions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [250]
status: new
---

## 関連
- **CWE-250: Execution with Unnecessary Privileges**  
  [CWE-250 Link](https://cwe.mitre.org/data/definitions/250.html)

## 説明
フォールバック関数の安全でない使用はフォールバック関数が不適切に使用された場合に生じる脆弱性を指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 意図しない実行による資金やデータの喪失。
- フォールバック関数が任意のインタラクションを許可している場合のコントラクトロジックの悪用。

フォールバック関数はコントラクトが一致する関数シグネチャを持たない呼び出しを受け取る場合にトリガーされます。これらは Ether の転送を処理したり、不明な関数呼び出しをプロキシするために使用できます。誤って構成された場合、不正アクセスや資金喪失など、意図しない動作を有効にする可能性があります。

## 対策
- **フォールバック関数内でのロジック実行を制限する**: 必要な場合を除き、フォールバック関数内に重要な実行ロジックを配置することを避けます。
- **Ether 受信を分離する**: Ethre 転送の処理には、`fallback()` をオーバーロードするのではなく、明示的に `receive()` を使用します。
- **呼び出しを検証する**: フォールバック関数が意図しない動作を実行しないことを確認します。必要に応じて、特定のユースケース (制御されたプロキシ呼び出しなど) に対してアクセス制御を使用します。
- **明示的な関数定義を使用する**: 重要な操作にはフォールバック関数に依存するのではなく、適切なアクセス制御を備えた明示的な関数を定義します。

## 事例
### 🚨 **安全でないフォールバック関数**
#### *問題点: アクセス制御やバリデーションがなく、意図しない実行が可能となっている。*
```solidity
pragma solidity ^0.8.0;

contract InsecureFallback {
    fallback() external {
        // No access control or validation
    }
}
```
- This contract allows any call with an unknown function signature to trigger the fallback function, which could result in unintended behavior.

### 安全なフォールバック関数 (Ether の安全な処理)
- Solution: Explicitly handle Ether transfers using receive()

```solidity
pragma solidity ^0.8.0;

contract SecureFallback {
    address public admin;

    constructor(address _admin) {
        admin = _admin;
    }

    // Explicitly define a receive function to safely accept Ether
    receive() external payable {}

    // Safe fallback function (does nothing if unintentionally called)
    fallback() external payable {
        // Optional: Log unexpected calls for security monitoring
    }
}
```
This version properly separates Ether reception from fallback execution, preventing unintended behavior.

---
