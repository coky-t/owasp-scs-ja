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
Always validate nonce values to ensure that they are correctly incremented and avoid reusing nonces. Ensure that nonce handling is robust, especially in cases where external calls are involved, to prevent replay attacks or transaction malleability.

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

Why is this better?
- Nonce is no longer user input: It is validated as part of a signed message, preventing arbitrary replay attempts.
- Prevents transaction replay attacks: By signing the message, an attacker cannot reuse an old transaction because the signature includes the nonce.
- Ensures integrity & authenticity: The contract only processes the transaction if the signed message is valid and matches the expected sender.

---
