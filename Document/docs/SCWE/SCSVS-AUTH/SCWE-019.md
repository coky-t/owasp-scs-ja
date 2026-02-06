---
title: 安全でない署名検証 (Insecure Signature Verification)
id: SCWE-019
alias: insecure-signature-verification
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [347]
status: new
---

## 関連
- CWE-347: Improper Verification of Cryptographic Signature  
  [https://cwe.mitre.org/data/definitions/347.html](https://cwe.mitre.org/data/definitions/347.html)

## 説明
安全でない署名検証は、コントラクトが暗号署名を不適切に検証する場合または署名を安全に検証できない場合に発生し、攻撃者がそれらを偽造や操作できるになります。この脆弱性は、不正なトランザクションや重要なセキュリティメカニズムのバイパスを可能にし、詐欺、不正アクセス、その他の攻撃につながる可能性があります。

安全でない署名検証のよくある原因には以下があります。
- 署名者のアドレスを検証していない。
- 弱いあるいは古い暗号ライブラリを使用している。
- アクションを処理する前に署名の有効性をチェックしていない。
- 署名形式を不適切に処理している。

## 対策
- **署名を適切に検証する:** 署名が意図した署名者と一致することを常に検証します。そのためには Ethereum アドレスに対して `ecrecover` 関数を使用し、その結果を想定した署名者アドレスと比較します。
- **強力な暗号手法を使用する:** 堅牢な暗号技法とライブラリを使用します。古いまたは弱いアルゴリズムの使用を避けます。
- **安全な署名形式を使用する:** 署名形式が適切に検証されていることを確認します (例: Ethereum 署名の `v`, `r`, `s` 値を適切に処理することを確認します)。
- **署名に基づいて動作する前のチェックを実装する:** 署名により影響を受ける可能性のあるロジックを実行する前に、常に署名と関連パラメータが有効であることのチェックを実行します。

## 事例

### 安全でない署名検証

```solidity
pragma solidity ^0.8.0;

contract InsecureSignatureExample {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    // Insecure signature verification: does not properly validate the signature
    function executeTransaction(bytes32 hash, uint8 v, bytes32 r, bytes32 s) public {
        address signer = ecrecover(hash, v, r, s);  // Signature verification
        if (signer == owner) {
            // Execute some sensitive action
        }
    }
}
```
安全でないバージョンでは、コントラクトは署名が所有者に対応しているかどうかをチェックしますが、その形式や正確性など、署名に関する潜在的な問題を適切に検証または処理しません。

### 修正した署名検証
```solidity
pragma solidity ^0.8.0;

contract SecureSignatureExample {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    // Secure signature verification: properly checks signature and signer
    function executeTransaction(bytes32 hash, uint8 v, bytes32 r, bytes32 s) public {
        address signer = ecrecover(hash, v, r, s);
        require(signer == owner, "Invalid signature");  // Ensure valid signature before proceeding
        // Execute some sensitive action
    }
}
```
修正したバージョンでは、`require()` 関数を使用して署名が所有者のアドレスと一致することを確認し、不正なアクセスやアクションを防ぐことでセキュリティを向上します。
