---
title: 署名変更可能性 (Signature Malleability)
id: SCWE-054
alias: signature-malleability
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-2]
  cwe: [345]
status: new
---

## 関連
- CWE-345: Insufficient Verification of Data Authenticity  
  [https://cwe.mitre.org/data/definitions/345.html](https://cwe.mitre.org/data/definitions/345.html)

## 説明
署名変更可能性は有効な署名の有効性を変えずに改変できる可能性を指します。これは、署名が適切に検証されていない場合、または署名の特定の構成要素 (`v`, `r`, `s` の値など) が改変されても有効な署名が生成される場合に発生します。

攻撃者は署名の `s` の値を変更したり、`v` を 27 と 28 の間でフリップすることで、同じメッセージに対して異なる有効な署名を生成できます。これは以下につながる可能性があります。

- リプレイ攻撃: トランザクションが改変された署名で再実行される可能性があります。
- トランザクションハイジャック: 有効な署名が改変され、資金をリダイレクトしたりコントラクトの状態を操作する可能性があります。

## 対策
署名の変更可能性を緩和するには、署名検証プロセスが堅牢であることを確保します。変更可能性を防ぐための追加チェックを備えた ECDSA や EdDSA など、署名検証を適切に処理する安全な暗号ライブラリを使用します。署名を検証する際は、署名の構成要素に正規表現を使用することを検討し、変更可能性を避けます。

### 脆弱なコントラクトの例 - (署名変更可能性を許可する)
```solidity
contract MalleableSignatureExample {
    function verifySignature(bytes32 message, uint8 v, bytes32 r, bytes32 s) public pure returns (bool) {
        address signer = ecrecover(message, v, r, s);  // Signature malleability risk
        return signer != address(0);
    }
}
```
- このコントラクトは `s` 値を制限せず、変更可能性のある署名を可能にします。
- 問題点: このコントラクトは `s` が `curve (s < secp256k1n/2)` の下半分にあるかどうかをチェックせず、同じメッセージに対して複数の有効な署名を可能にします。



### 修正したコントラクトの例 - (署名変更可能性を防止する)
```solidity
  pragma solidity ^0.8.0;

  import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

  contract SecureSignatureExample {
      using ECDSA for bytes32;

      function verifySignature(bytes32 message, bytes memory signature) public pure returns (address) {
          return message.toEthSignedMessageHash().recover(signature);
      }
  }
```

- 修正内容: `OpenZeppelin の ECDSA ライブラリ` を使用します。これは `s` が下半分にあることを確保し、`v` を 27 または 28 に制限します。
- 結果: 攻撃者が有効な署名を改変して、代わりの同等に有効なものを作成することを防ぎます。
