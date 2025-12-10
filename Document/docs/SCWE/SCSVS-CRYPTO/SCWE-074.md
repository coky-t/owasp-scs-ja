---
title: 複数の可変長引数によるハッシュ衝突 (Hash Collisions with Multiple Variable Length Arguments)
id: SCWE-074
alias: hash-collisions-multiple-variable-length-arguments
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-2]
  cwe: [347]
status: new
---

## 関連
- CWE-347: Improper Verification of Cryptographic Signature  
  [https://cwe.mitre.org/data/definitions/347.html](https://cwe.mitre.org/data/definitions/347.html)

## 説明
複数の可変長引数をハッシュ化する場合、ハッシュ衝突の潜在的なリスクがあります。これは、二つの異なる入力セットが同じハッシュ出力を生成する場合に発生する可能性があります。この脆弱性は、ハッシュが識別子、署名、鍵として使用される状況で特に顕著であり、衝突は、署名の誤ったバリデーションや予期しないコントラクトロジックの実行など、不正な動作やセキュリティ侵害につながる可能性があります。

## 対策
ハッシュ衝突を防ぐには、`keccak256` や `sha256` などの強力な暗号ハッシュ関数を使用し、ハッシュ関数への入力が適切に構造化されていることを確保します。エントロピーのさまざまなソース (タイムスタンプ、ノンスなど) を組み合わせることを検討し、ハッシュを信頼する前に常にデータの完全性を検証します。

### 脆弱なコントラクトの例
```solidity
contract Example {
    function verifyData(bytes memory data1, bytes memory data2) public pure returns (bytes32) {
        // Vulnerable to hash collisions with multiple variable-length arguments
        return keccak256(abi.encodePacked(data1, data2));  // Possible collision risk
    }
}
```


### 修正したコントラクトの例
```solidity
contract Example {
    function verifyData(bytes memory data1, bytes memory data2) public pure returns (bytes32) {
        // Safe way to hash multiple variable-length arguments
        return keccak256(abi.encode(data1, data2));  // More secure handling of variable-length inputs
    }
}
```
