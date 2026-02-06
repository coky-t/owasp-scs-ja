---
id: SCSTG-TEST-0013
scsvs_cg_id:
- SCSVS-CRYPTO
scsvs_scg_id:
- SCSVS-CRYPTO-2
platform: []
title: 安全な署名検証 (Secure Signature Verification)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0013
---

### **説明**
暗号化の実践は、分散システム内のインタラクションの機密性、完全性、真正性を確保する上で不可欠です。暗号化実装、鍵管理、署名検証における弱点は、不正アクセス、データの不正操作、トランザクションのセキュリティを侵害する攻撃などの脆弱性につながる可能性があります。このテストは、スマートコントラクトにおける鍵管理、署名検証、安全な乱数生成など、安全な暗号化の実践を検証することに重点を置いています。

---

### **テスト 1: 安全な署名検証を検証する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract SignatureVerification {
    function verifySignature(address _signer, bytes32 _message, bytes memory _signature) public pure returns (bool) {
        // Directly using ecrecover, without checking for message format, leads to potential attack vectors
        address recovered = ecrecover(_message, uint8(_signature[0]), bytes32(_signature[1]), bytes32(_signature[2]));
        return recovered == _signer;
    }
}
```

#### **なぜ脆弱なのか**

- コントラクトはメッセージ構造を検証せずに直接 `ecrecover` を使用します。
- 攻撃者は、別のメッセージの署名を再使用して異なるトランザクションを認証することで、リプレイ攻撃を行う可能性があります。
- 適切なチェックの欠如は、攻撃者が署名を偽造またはリプレイできる攻撃に対して、コントラクトが脆弱になります。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract SecureSignatureVerification {
    // Use of EIP-712 for standard signature verification with specific message formats
    function verifySignature(address _signer, bytes32 _message, bytes memory _signature) public pure returns (bool) {
        bytes32 messageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", _message));
        address recovered = ecrecover(messageHash, uint8(_signature[0]), bytes32(_signature[1]), bytes32(_signature[2]));
        return recovered == _signer;
    }
}

```

### **チェック方法**
- **コードレビュー:** 署名が EIP-712 などの標準を使用して適切に検証されていること、およびコントラクトが `ecrecover` を安全に使用していることを検証します。リカバリ前に、メッセージがハッシュ化され、正しくプレフィックス付けされていることを確認します。
- **動的テスト:** リプレイされた署名や不正な署名でテストして、コントラクトがそのようなトランザクションを拒否することを検証します。


---

#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract RandomNumberGenerator {
    uint256 public randomValue;

    function generateRandomNumber() public {
        randomValue = uint256(keccak256(abi.encodePacked(block.timestamp, block.difficulty)));
    }
}
```


#### **なぜ脆弱なのか**

- コントラクトは `block.timestamp` や `block.difficulty` (post-merge: `block.prevrandao`) などのブロックプロパティを使用して乱数を生成しますが、バリデーターによって不正操作される可能性があります。
- この弱い乱数生成は予測可能な値につながる可能性があり、くじやギャンブルの結果の不正操作などの攻撃に対してコントラクトが脆弱になります。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";
import "@chainlink/contracts/src/v0.8/interfaces/VRFCoordinatorV2Interface.sol";

contract SecureRandomNumberGenerator is VRFConsumerBaseV2 {
    VRFCoordinatorV2Interface immutable COORDINATOR;
    uint64 immutable s_subscriptionId;
    bytes32 immutable s_keyHash;
    uint256 public randomValue;
    uint256 public s_requestId;

    constructor(
        address vrfCoordinator,
        uint64 subscriptionId,
        bytes32 keyHash
    ) VRFConsumerBaseV2(vrfCoordinator) {
        COORDINATOR = VRFCoordinatorV2Interface(vrfCoordinator);
        s_subscriptionId = subscriptionId;
        s_keyHash = keyHash;
    }

    function requestRandomNumber() external {
        s_requestId = COORDINATOR.requestRandomWords(
            s_keyHash,
            s_subscriptionId,
            3,      // requestConfirmations
            100000, // callbackGasLimit
            1      // numWords
        );
    }

    function fulfillRandomWords(uint256, uint256[] memory randomWords) internal override {
        randomValue = randomWords[0];
    }
}

```

### **チェック方法**
- **コードレビュー:** 乱数がブロックプロパティなどの予測可能な値から導出されていないことを確認します。`Chainlink VRF` などの外部の安全なソースがランダム性に使用されているかどうかをチェックします。
- **動的テスト:** 敵対的な条件下でコントラクトの動作をテストし、ランダム性が予測または不正操作できないことを確認します。
