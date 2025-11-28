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
- **Denial of Service:** Destination chain message execution reverts due to insufficient gas, preventing critical state changes or callbacks.
- **Message Backlog/Failed Payloads:** Messages accumulate in failed payload storage/queues, increasing operational burden.
- **Operational Risk:** Requires manual retries, clearing, or rescue flows to restore liveness; prolonged outages can impact user operations (e.g., bridging, synchronization, DeFi actions).

## 対策
- **Validate minimum gas limit:** Decode LayerZero adapter parameters and enforce a protocol-defined minimum gas limit per message type.
- **Use safe defaults:** Provide default adapter params with adequate gas and reject user-supplied values below the threshold.
- **Granular minima:** If different message types have varying complexity, define per-endpoint or per-function minimum gas values.
- **Robust failure handling:** Implement operational procedures and tooling to safely retry or clear failed payloads without compromising security.

## 事例
🧪  Example

❌ Vulnerable Code (Lack of gas limit validation allows pathway blocking)
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

✅ Safe Code (Enforces minimum gas limit to prevent blocking)
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
