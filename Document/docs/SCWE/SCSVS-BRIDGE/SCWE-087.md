---
title: クロスチェーンメッセージングにおけるペイロードサイズ検証の欠如 (サービス拒否/資金の滞留) (Missing Payload Size Validation in Cross-Chain Messaging (Denial of Service/Stuck Funds))
id: SCWE-087
alias: Missing-Payload-Size-Validation-in-Cross-Chain-Messaging
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-2]
  cwe: [1284]
status: new
---

## 関連
- CWE-1284: Improper Validation of Specified Quantity in Input  
  [CWE-1284 Link](https://cwe.mitre.org/data/definitions/1284.html)
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
この弱点は、クロスチェーンメッセージングを実行するスマートコントラクトがペイロードを送出や送信する前にそのサイズを検証できない場合に発生します。リレイヤーベースのメッセージングを使用するプロトコル (LayerZero, Wormhole など) では、データは一般的にソースチェーン上でペイロードとしてエンコードおよび送出され、その後、宛先チェーン上でリレーおよびデコードされます。エンコードされたペイロードが最大許容サイズ (ブリッジ/メッセージングプロトコルによって異なりますが、通常は 10,000 バイト程度) を超えると、メッセージの送信やデコードが元に戻る可能性があります。これは、大きなペイロードが処理中に out-of-gas 消費を引き起こしたり、明示的なサイズチェックに失敗することがよくあるためです。lock-mint アーキテクチャでは、宛先チェーンで資産をアンロックやミントできないため、ユーザー資金の永久的な喪失につながる可能性があります。
## 影響
- **サービス拒否:** 大きすぎるペイロードはクロスチェーン転送時に元に戻り、宛先チェーンの正当な状態更新やトークンのミント/アンロックを妨げます。
- **資金のスタックや喪失:** lock-and-mint または burn-and-mint 設計を使用するブリッジでは、トランザクションの失敗を繰り返すと、宛先でリリースまたはクレームする方法なしで、ユーザーの資産がソースチェーンでロックされる可能性があります。
- **運用リスク:** 悪意のある、または意図しない、サイズの大きすぎるペイロードの送出が使用されると、ブリッジの運用を妨害し、プロトコルの生存性を阻害し、重要なビジネスロジックにアクセスできなくなる可能性があります。
## 対策
- ソースチェーンと宛先チェーンの両方で最大ペイロードサイズバリデーションを強制します。
- ペイロードのエンコーディングとデコーディングに関する防御コーディングを実行し、out-of-gas や out-of-bound エラーを捕捉します。
- このような障害が発生した際に、資金を安全に返金またはアンロックするためのフォールバックまたはリバース処理戦略を検討します。
## 事例
🧪  **例: `lockTokens()` でのユーザー提供の Merkle 証明**

❌ **脆弱なコード (ペイロードサイズバリデーションなし)**
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// SourceChain.sol
event Locked(address indexed user, uint256 amount, bytes payload);
function lockTokens(uint256 amount, bytes calldata merkleProof) external {
    require(amount > 0, "Invalid amount");
    // Lock tokens (ERC20 transferFrom)
    require(token.transferFrom(msg.sender, address(this), amount), "Transfer failed");
    // Encode user, amount, timestamp, and the supplied Merkle proof
    bytes memory payload = abi.encode(msg.sender, amount, block.timestamp, merkleProof);
    // ⚠️ No payload size validation!
    emit Locked(msg.sender, amount, payload);
}
```
宛先チェーン: デコードとミント
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// DestinationChain.sol
function mintFromPayload(bytes calldata payload) external {
    // Will revert if payload is too large for decode!
    (address user, uint256 amount, uint256 timestamp, bytes memory merkleProof) =
        abi.decode(payload, (address, uint256, uint256, bytes));
    // Verification of Merkle proof, then mint tokens
    _mint(user, amount);
}
```
✅  安全なコード (ペイロードサイズバリデーションあり)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// SourceChain.sol
event Locked(address indexed user, uint256 amount, bytes payload);
function lockTokens(uint256 amount, bytes calldata merkleProof) external {
    require(amount > 0, "Invalid amount");
    // Lock tokens (ERC20 transferFrom)
    require(token.transferFrom(msg.sender, address(this), amount), "Transfer failed");
    // Encode user, amount, timestamp, and the supplied Merkle proof
    bytes memory payload = abi.encode(msg.sender, amount, block.timestamp, merkleProof);
    // Check payload size
    require(payload.length <= 10_000, "Payload exceeds max allowed size");
    emit Locked(msg.sender, amount, payload);
}
```
宛先チェーン: デコードとミント (オプションで二重チェック)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// DestinationChain.sol
function mintFromPayload(bytes calldata payload) external {
    require(payload.length <= 10_000, "Payload too large");
    (address user, uint256 amount, uint256 timestamp, bytes memory merkleProof) =
        abi.decode(payload, (address, uint256, uint256, bytes));
    // Proceed with Merkle proof verification and minting
    _mint(user, amount);
}

```
