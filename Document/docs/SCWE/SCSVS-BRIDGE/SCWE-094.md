---
title: LayerZero メッセージ送信における不十分なガス制限バリデーション (Insufficient Gas Limit Validation in LayerZero Message Sending)
id: SCWE-094
alias: insufficient-gas-limit-validation-layerzero
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
この弱点は、クロスチェーン通信に LayerZero を使用するスマートコントラクトがアダプタパラメータの最小ガス制限の検証に失敗した場合に発生します。攻撃者 (または設定ミスのあるクライアント) は、宛先チェーンの実行に対して低すぎるアダプタパラメータのガス値を指定できます。結果として、メッセージ処理はビジネスロジックやエラー処理に到達する前に元に戻り、メッセージは宛先チェーン上で失敗したペイロードとして記録されます。失敗したペイロードが蓄積すると、手動による介入が行われるまでクロスチェーン通信経路をブロックして、プロトコルの生存性を損なう可能性があります。

## 影響
- **サービス拒否:** 宛先チェーンメッセージ実行は不十分なガスにより元に戻り、重要な状態変更やコールバックを妨げます。
- **メッセージバックログ/失敗したペイロード:** メッセージが失敗したペイロードのストレージ/キューに蓄積し、運用上の負担を増します。
- **運用リスク:** 稼働状態を回復するには、手動での再試行、クリア、またはレスキューフローを必要とします。長時間の停止はユーザー操作 (ブリッジ、同期、DeFi アクションなど) に影響を及ぼす可能性があります。

## 対策
- **最小ガス制限を検証する:** LayerZero アダプタパラメータをデコードし、メッセージタイプごとにプロトコル定義の最小ガス制限を適用します。
- **安全なデフォルトを使用する:** デフォルトのアダプタパラメータに適切なガスを提供し、閾値を下回るユーザー指定の値を拒否します。
- **粒度の最小値:** メッセージタイプごとに複雑さが異なる場合は、エンドポイントごとまたは関数ごとに最小ガス値を定義します。
- **堅牢な障害処理:** セキュリティを損なうことなく、失敗したペイロードを安全に再試行またはクリアするための運用手順とツールを実装します。

## 事例
🧪 例

❌ 脆弱なコード (ガス制限バリデーションの欠落は経路をブロックします)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@layerzerolabs/solidity-examples/contracts/lzApp/NonblockingLzApp.sol";

contract VulnerableLzApp is NonblockingLzApp {
    constructor(address _endpoint) NonblockingLzApp(_endpoint) {}

    function sendMessage(uint16 _dstChainId, bytes calldata _payload, bytes calldata _adapterParams) external payable {
        // No validation on _adapterParams, attacker can specify low gas
        _lzSend(_dstChainId, _payload, payable(msg.sender), address(0), _adapterParams, msg.value);
    }
}
```

✅ 安全なコード (最小ガス制限を適用してブロックを防ぎます)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@layerzerolabs/solidity-examples/contracts/lzApp/NonblockingLzApp.sol";

contract FixedLzApp is NonblockingLzApp {
    uint256 public constant MIN_GAS_LIMIT = 200000;

    constructor(address _endpoint) NonblockingLzApp(_endpoint) {}

    function sendMessage(uint16 _dstChainId, bytes calldata _payload, bytes calldata _adapterParams) external payable {
        require(_extractGasLimit(_adapterParams) >= MIN_GAS_LIMIT, "low gas");
        _lzSend(_dstChainId, _payload, payable(msg.sender), address(0), _adapterParams, msg.value);
    }

    function _extractGasLimit(bytes calldata _adapterParams) internal pure returns (uint256 gasLimit) {
        require(_adapterParams.length >= 34, "bad params");
        assembly { gasLimit := calldataload(add(_adapterParams.offset, 2)) }
    }
}
```
