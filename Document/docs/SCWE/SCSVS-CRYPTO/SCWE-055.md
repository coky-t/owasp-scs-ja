---
title: 署名リプレイ攻撃に対する保護の欠如 (Missing Protection against Signature Replay Attacks)
id: SCWE-055
alias: missing-signature-replay-protection
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-1]
  cwe: [294]
status: new
---

## 関連
- CWE-294: Authentication Bypass by Capture-replay  
  [https://cwe.mitre.org/data/definitions/294.html](https://cwe.mitre.org/data/definitions/294.html)

## 説明
署名リプレイ攻撃は、以前のトランザクションの有効な署名が、異なるトランザクションやコントラクト呼び出しなど、異なるコンテキストで再使用される場合に発生します。適切な保護なしでは、攻撃者は署名をキャプチャしてリプレイし、認証チェックをバイパスして不正なアクションやトランザクションを引き起こす可能性があります。

## 対策
署名リプレイ攻撃を防ぐには、署名が特定のトランザクションまたはコンテキストに対して有効であることを確認するための追加のチェックを組み込みます。これは、署名にノンス、タイムスタンプ、または一意のトランザクションハッシュなどの一意の識別子を組み込み、特定の用途に結びつけることで実現できます。署名が意図したアクションに対してのみ有効であることを常に検証します。

### 脆弱なコントラクトの例
```solidity
contract ReplayAttackExample {
    mapping(address => uint256) public nonces;

    function authenticate(bytes32 message, uint8 v, bytes32 r, bytes32 s) public {
        address signer = ecrecover(message, v, r, s);
        require(signer == msg.sender, "Invalid signature");
        // No protection against replay attacks; attacker can reuse the same signature
    }
}
```

### 修正したコントラクトの例
```solidity
  pragma solidity ^0.8.0;

  import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

  contract SecureReplayProtectionExample {
      using ECDSA for bytes32;

      mapping(address => uint256) public nonces;

      function authenticate(bytes32 message, bytes memory signature) public {
          uint256 nonce = nonces[msg.sender]++;
          bytes32 messageWithContext = keccak256(abi.encodePacked(message, nonce, block.chainid));  
          address signer = messageWithContext.toEthSignedMessageHash().recover(signature);
          require(signer == msg.sender, "Invalid signature");
      }
  }
```
**実装した修正内容:**
- 署名をノンスにバインドします (一度しか使用できないようにします)。
- `chainId` を組み込みます (クロスチェーンリプレイ攻撃を防ぎます)。
- OpenZeppelin の ECDSA ライブラリを使用します (署名変更可能性のリスクを回避します)。

---
