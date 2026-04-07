---
title: 弱い VRF パラメータ化またはコールバックバリデーション (Weak VRF Parameterization or Callback Validation)
id: SCWE-115
alias: weak-vrf-validation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-1]
  cwe: [330]
status: new
---

## 関連
- CWE-330: Use of Insufficiently Random Values  
  [https://cwe.mitre.org/data/definitions/330.html](https://cwe.mitre.org/data/definitions/330.html)

## 説明
`requestId`、送信者、サブスクリプションを検証せずに VRF サービスを使用すると、攻撃者が処理を偽装したり、サブスクリプション残高を流出する可能性があります。キーハッシュ、ガス制限、または共有サブスクリプションを構成ミスすると、予測可能な障害を引き起こし、ランダム性を漏洩するフォールバックロジックを強制する可能性があります。

## 対策
- VRF の履行を、追跡される `requestId` 値と信頼できるコーディネーターアドレスに結びつけます。
- 専用のサブスクリプション、適切なキーハッシュ、妥当なコールバックガス制限を使用します。
- 予期しない履行を元に戻し、フォールバック擬似乱数の使用を避けます。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;
import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";

contract Lottery is VRFConsumerBaseV2 {
    uint256 public randomWord;

    function fulfillRandomWords(uint256, uint256[] memory words) internal override {
        randomWord = words[0]; // no requestId or sender validation
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";

contract Lottery is VRFConsumerBaseV2 {
    uint256 public pending;

    function request() external { pending = COORDINATOR.requestRandomWords(/* params */); }

    function fulfillRandomWords(uint256 requestId, uint256[] memory words) internal override {
        require(requestId == pending, "unknown request");
        pending = 0;
        // use words[0]...
    }
}
```
