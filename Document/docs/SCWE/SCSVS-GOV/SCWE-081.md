---
title: Nonce の不適切な取り扱い (Improper Handling of Nonce)
id: SCWE-081
alias: improper-handling-of-nonce
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
nonce 値は、トランザクションが正しい順序で処理されることを確保し、リプレイ攻撃を防ぐために使用されます。nonce の不適切な取り扱いやバリデーションは、トランザクションリプレイやトランザクションの不適切な順序付けのような問題を引き起こし、攻撃者に悪用される可能性があります。

## 対策
nonce 値が正しくインクリメントされていることを確認するために常に検証し、nonce の再使用を避けます。特に外部呼び出しが関係する場合、nonce の取り扱いが堅牢であることを確認し、リプレイ攻撃やトランザクションの可鍛性を防ぎます。

### 脆弱なコントラクトの例
```solidity
contract Example {
    mapping(address => uint256) public nonces;

    function transfer(address _to, uint256 _amount) public {
        uint256 nonce = nonces[msg.sender]; // Nonce is not validated properly
        nonces[msg.sender] = nonce + 1;     // This could allow replay attacks
        // Transfer logic
    }
}
```
### 修正したコントラクトの例
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SecureExample {
    mapping(address => uint256) public nonces;

    function transfer(
        address _to,
        uint256 _amount,
        uint256 _nonce,
        bytes memory _signature
    ) public {
        require(_nonce == nonces[msg.sender], "Invalid nonce");

        // Hash the message
        bytes32 messageHash = keccak256(abi.encodePacked(msg.sender, _to, _amount, _nonce));
        bytes32 ethSignedMessageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", messageHash));

        // Recover the signer's address
        address signer = recoverSigner(ethSignedMessageHash, _signature);
        require(signer == msg.sender, "Invalid signature");

        nonces[msg.sender] = _nonce + 1;
        // Transfer logic
    }

    function recoverSigner(bytes32 _ethSignedMessageHash, bytes memory _signature) internal pure returns (address) {
        (bytes32 r, bytes32 s, uint8 v) = splitSignature(_signature);
        return ecrecover(_ethSignedMessageHash, v, r, s);
    }

    function splitSignature(bytes memory sig) internal pure returns (bytes32 r, bytes32 s, uint8 v) {
        require(sig.length == 65, "Invalid signature length");
        assembly {
            r := mload(add(sig, 32))
            s := mload(add(sig, 64))
            v := byte(0, mload(add(sig, 96)))
        }
    }
}
```

なぜこれが優れているのか？
- nonce はもはやユーザー入力ではありません。著名付きメッセージの一部として検証され、任意のリプレイ試行を防ぎます。
- トランザクションリプレイ攻撃を防ぎます。メッセージに署名することで、署名が nonce を含むため、攻撃者は古いトランザクションを再使用できません。
- 完全性と真正性を確保します。署名付きメッセージが有効で、想定した送信者と一致する場合にのみ、コントラクトはトランザクションを処理します。

---
