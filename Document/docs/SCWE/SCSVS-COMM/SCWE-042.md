---
title: 外部呼び出しの安全でない使用 (Insecure Use of External Calls)
id: SCWE-042
alias: insecure-use-of-external-calls
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-2]
  cwe: [829]
status: new
---

## 関連
- CWE-829: Inclusion of Functionality from Untrusted Control Sphere  
  [CWE-829 Link](https://cwe.mitre.org/data/definitions/829.html)

## 説明
外部呼び出しの安全でない使用は、適切なバリデーションやセーフガードなしで外部コントラクトを呼び出す際に生じる脆弱性を指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- 呼び出されたコントラクトの脆弱性の悪用。

## 対策
- **ターゲットを検証する:** ターゲットコントラクトが信頼でき、安全であることを確認します。
- **安全なライブラリを使用する:** 外部呼び出しには十分に監査されたライブラリを活用します。
- **徹底的にテストする:** 広範なテストを実施して、外部呼び出しが安全であることを確認します。

## 事例
- **安全でない外部呼び出し**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureExternalCall {
        function callExternal(address target, bytes memory data) public {
            (bool success, ) = target.call(data); // No validation
            require(success, "Call failed");
        }
    }
    ```

- **安全な外部呼び出し**
    ```solidity
    pragma solidity ^0.8.0;

    interface ITrustedContract {
        function executeAction(uint256 param) external;
    }

    contract SecureExternalCall {
        address public immutable trustedTarget;
        address public owner;

        constructor(address _trustedTarget) {
            trustedTarget = _trustedTarget;
            owner = msg.sender;
        }

        modifier onlyOwner() {
            require(msg.sender == owner, "Unauthorized");
            _;
        }

        function callExternal(uint256 param) public onlyOwner {
            ITrustedContract(trustedTarget).executeAction(param);
        }
    }
    ```
なぜこれが安全なのですか？
- 任意のデータ入力なし - 事前定義済みの `function executeAction(uint256)` のみを呼び出せます。
- アクセス制御の追加 - 所有者のみが外部呼び出しを実行できます。
- インタフェースベースの呼び出し - 生の `.call()` を避けて、攻撃ベクトルを軽減します。
- 不変の信頼できるアドレス — ターゲットコントラクトへの実行時改変を防ぎます。

---
