---
title: 安全でないクロスチェーンメッセージング (Insecure Cross-Chain Messaging)
id: SCWE-034
alias: insecure-cross-chain-messaging
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
安全でないクロスチェーンメッセージングは異なるブロックチェーン間の通信時に発生する脆弱性を指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資産やデータの喪失。
- クロスチェーンロジックでの脆弱性の悪用。

- メッセージハンドラでの未検証の msg.value による経済的な不整合。

## 対策
- **メッセージを検証する:** すべてのクロスチェーンメッセージが適切に検証されていることを確保します。
- **安全なプロトコルを使用する:** 安全なクロスチェーン通信プロトコルを活用します。
- **徹底的にテストする:** 広範なテストを実施して、クロスチェーンロジックが安全であることを確保します。

- **msg.value を検証する:** メッセージペイロードやプロトコルパラメータから期待値をデコードし、`msg.value` がこの期待値と一致する (または満たす) ことを要求します。不一致の場合は元に戻します。

## 事例
- **安全でないクロスチェーンメッセージング**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureCrossChain {
        event MessageProcessed(bytes message);

        function processMessage(bytes memory message) public {
            // ❌ No validation of the sender (anyone can call this function!)
            // ❌ No signature verification (attackers can inject fake messages!)
            // 🚨 No relayer authorization
            // 🚨 No replay protection
            emit MessageProcessed(message);
        }
    }
    ```
🔴 問題点: この関数はバリデーションなしでメッセージを受け入れ、不正なトランザクションを許可します。

不正な中継者 (誰でも呼び出し可能！)
- 問題点: この関数は msg.sender が processMessage() を呼び出すことを許可しており、攻撃者が任意のメッセージを送信できることを意味します。
- 影響: この関数がクロスチェーン資産移転に接続されている場合、攻撃者はトランザクションを偽造したり、意図しないアクションをトリガーしたり、資産を流出できます。

署名検証なし (偽のメッセージ)
- 問題点: このコントラクトはメッセージの真正性を検証しません。
- 影響: 攻撃者は偽のメッセージを注入し、コントラクトを騙して不正なアクションを実行します。

リプレイ攻撃
- 問題点: このコントラクトは処理されたメッセージを追跡しません。
- 影響: 同じメッセージが複数回再生され、機密性の高い操作の繰り返し実行につながる可能性があります。

- **安全でないクロスチェーンメッセージング**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureCrossChain {
        mapping(address => bool) public trustedRelayers;
        mapping(bytes32 => bool) public processedMessages;

        event MessageProcessed(bytes32 indexed messageHash, address indexed sender);
        event RelayerUpdated(address relayer, bool status);

        address public owner;

        modifier onlyOwner() {
            require(msg.sender == owner, "Not the owner");
            _;
        }

        constructor(address[] memory initialRelayers) {
            owner = msg.sender;
            for (uint i = 0; i < initialRelayers.length; i++) {
                trustedRelayers[initialRelayers[i]] = true;
            }
        }

        function setRelayer(address relayer, bool status) external onlyOwner {
            trustedRelayers[relayer] = status;
            emit RelayerUpdated(relayer, status);
        }

        function processMessage(
            bytes memory message, 
            uint8 v, bytes32 r, bytes32 s
        ) public {
            require(trustedRelayers[msg.sender], "Unauthorized relayer");

            bytes32 messageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", keccak256(message)));
            address signer = ecrecover(messageHash, v, r, s);
            require(signer != address(0), "Invalid signature");

            require(!processedMessages[messageHash], "Message already processed");
            processedMessages[messageHash] = true;

            emit MessageProcessed(messageHash, signer);

            // ✅ Securely process the message
        }
    }
    ```

修正内容: 署名検証、リレイヤーバリデーション、リプレイ保護を実装します。
なぜこれが適切なのか？  
✅ 署名を適切に検証する: Ethereum 署名メッセージのハッシュ化で ecrecover() を使用します。
✅ 管理者がリレイヤーを管理できる: setRelayer() を介して動的にリレイヤーを更新できます。
✅ リプレイ攻撃を防止する: 処理されたメッセージを processedMessages マッピングで追跡します。
✅ メッセージの真正性を確保する: 有効に署名されたメッセージのみが受け入れられます。

---

- **クロスチェーンメッセージ処理で未検証の msg.value**
    ```solidity
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    contract VulnerableBridgedGovernor {
        address public endpoint;
        uint256 public _lastNonce;
        uint32 public ownerEid;
        address public owner;

        struct Origin {
            uint32 srcEid;
            address sender;
            uint256 nonce;
        }

        struct Call {
            address to;
            uint256 value;
            bytes data;
        }

        modifier onlyProxy() {
            _;
        }

        function runCalls(Call[] memory calls) internal {
            for (uint i = 0; i < calls.length; i++) {
                (bool success, ) = calls[i].to.call{value: calls[i].value}(calls[i].data);
                require(success, "Call failed");
            }
        }

        function lzReceive(
            Origin calldata origin,
            bytes32, /* guid */
            bytes calldata message,
            address, /* executor */
            bytes calldata /* extraData */
        ) public payable onlyProxy {
            require(msg.sender == endpoint, "Must be called by the endpoint");
            require(origin.srcEid == ownerEid, "Invalid message source chain");
            require(origin.sender == owner, "Invalid message sender");
            require(origin.nonce == _lastNonce + 1, "Invalid message nonce");
            _lastNonce = origin.nonce;
            runCalls(abi.decode(message, (Call[])));
            // <-- No check on msg.value!
        }
    }
    ```
    🔴 問題点: ハンドラが任意の `msg.value` を受け入れ、プロトコルアカウンティングを同期ズレするフロントランニング呼び出しや意図しない値渡し呼び出しを可能にします。

- **クロスチェーンメッセージ処理で検証された msg.value**
    ```solidity
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    contract SafeBridgedGovernor {
        address public endpoint;
        uint256 public _lastNonce;
        uint32 public ownerEid;
        address public owner;

        struct Origin {
            uint32 srcEid;
            address sender;
            uint256 nonce;
        }

        struct Call {
            address to;
            uint256 value;
            bytes data;
        }

        modifier onlyProxy() {
            _;
        }

        function runCalls(Call[] memory calls) internal {
            for (uint i = 0; i < calls.length; i++) {
                (bool success, ) = calls[i].to.call{value: calls[i].value}(calls[i].data);
                require(success, "Call failed");
            }
        }

        function lzReceive(
            Origin calldata origin,
            bytes32, /* guid */
            bytes calldata message,
            address, /* executor */
            bytes calldata /* extraData */
        ) public payable onlyProxy {
            require(msg.sender == endpoint, "Must be called by the endpoint");
            require(origin.srcEid == ownerEid, "Invalid message source chain");
            require(origin.sender == owner, "Invalid message sender");
            require(origin.nonce == _lastNonce + 1, "Invalid message nonce");
            _lastNonce = origin.nonce;

            (uint256 expectedMsgValue, Call[] memory calls) = abi.decode(message, (uint256, Call[]));
            require(msg.value >= expectedMsgValue, "Invalid message value");
            runCalls(calls);
        }
    }
    ```
    ✅ 修正内容: ペイロードから期待値をデコードし、ダウンストリーム呼び出しを事項する前に `msg.value` がそれを満たすことを強制します。
