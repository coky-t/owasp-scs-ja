---
title: 適切な署名検証の欠如 (Lack of Proper Signature Verification)
id: SCWE-056
alias: lack-proper-signature-verification
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-1]
  cwe: [345]
status: new
---

## 関連
- CWE-345: Insufficient Verification of Data Authenticity  
  [https://cwe.mitre.org/data/definitions/345.html](https://cwe.mitre.org/data/definitions/345.html)

## 説明
この脆弱性は、署名が認可されたエンティティによって生成されたかどうかを、スマートコントラクトが適切に検証できない場合に発生します。
Ethereum では、コントラクトは署名のチェックに `ecrecover` を使用することがよくありますが、メッセージの署名者を検証できず、以下を可能にします。

- 不正なトランザクション、攻撃者が任意のキーから署名を送信します。
- リプレイ攻撃、有効な署名が再使用されアクションを繰り返します。
- 悪意のあるコントラクト状態の操作、攻撃者が不正アクセスを取得します。

## 対策
この脆弱性を緩和するには、常に安全な暗号手法を使用して適切な署名検証を実装します。`ecrecover` 関数を使用して署名者のアドレスを復元し、復元したアドレスが期待したアドレスと一致することを確認します。さらに、署名が意図したメッセージまたはトランザクションに対して有効であること、および署名者がアクションを実行するために認可されていることを検証します。

### 脆弱なコントラクトの例 - (署名者検証の欠如)
```solidity
contract SignatureVerificationExample {
    function authenticate(bytes32 message, uint8 v, bytes32 r, bytes32 s) public view returns (address) {
        address signer = ecrecover(message, v, r, s);
        return signer;  // No further validation of the signer or message
    }
}
```
- 問題点: このコントラクトは復元したアドレスが認可された署名者と一致するかどうかをチェックしていません。
- エクスプロイト: 有効な ECDSA 署名であれば、攻撃者の鍵からのものであっても、使用できます。


### 修正したコントラクトの例 - (適切な署名検証)
```solidity
    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

    contract SecureSignatureVerificationExample {
        using ECDSA for bytes32;

        address public authorizedSigner;

        constructor(address _authorizedSigner) {
            require(_authorizedSigner != address(0), "Invalid signer");
            authorizedSigner = _authorizedSigner;
        }

        function authenticate(bytes32 message, bytes memory signature) public view returns (bool) {
            address signer = message.toEthSignedMessageHash().recover(signature);
            require(signer == authorizedSigner, "Unauthorized signer"); // Proper validation
            return true;
        }
    }
```
- 修正内容: コントラクトは復元したアドレスが `authorizedSigner` と一致することを明示的にチェックするようにしました。
- 結果: 攻撃者が任意の署名を送信することを防ぎ、認可された署名のみが受け入れられるようになります。
