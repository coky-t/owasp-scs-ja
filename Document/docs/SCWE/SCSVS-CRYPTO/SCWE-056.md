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
To mitigate this vulnerability, always implement proper signature verification using secure cryptographic methods. Use the `ecrecover` function to recover the signer’s address and ensure that the recovered address matches the expected address. Additionally, verify that the signature is valid for the intended message or transaction and that the signer is authorized to perform the action.

### 脆弱なコントラクトの例 - (署名者検証の欠如)
```solidity
contract SignatureVerificationExample {
    function authenticate(bytes32 message, uint8 v, bytes32 r, bytes32 s) public view returns (address) {
        address signer = ecrecover(message, v, r, s);
        return signer;  // No further validation of the signer or message
    }
}
```
- Issue: The contract does not check if the recovered address matches an authorized signer.
- Exploit: Any valid ECDSA signature can be used, even from an attacker’s key.


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
- Fix: The contract now explicitly checks that the recovered address matches `authorizedSigner`.
- Outcome: Prevents attackers from submitting arbitrary signatures and ensures only authorized signatures are accepted.
