---
title: 安全でない Delegatecall の使用 (Insecure Delegatecall Usage)
id: SCWE-035
alias: insecure-delegatecall-usage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [250]
status: new
---

## 関連
- CWE-250: Execution with Unnecessary Privileges  
  [CWE-250 Link](https://cwe.mitre.org/data/definitions/250.html)

## 説明
安全でない delegatecall の使用は、`delegatecall` を使用して別のコントラクトからコードを実行する際に生じる脆弱性を指します。これは以下につながる可能性があります。
- 機密性の高い関数への不正アクセス。
- 呼び出されたコントラクトの脆弱性の悪用。
- 資金やデータの喪失。

## 対策
- **ターゲットを検証する:** ターゲットコントラクトが信頼でき、安全であることを確認します。
- **パーミッションを制限する:** `delegatecall` の使用を信頼できるアドレスに制限します。
- **徹底的にテストする:** 広範なテストを実施して、`delegatecall` が安全に使用されることを確認します。

## 事例
- **Insecure Delegatecall Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureDelegatecall {
        function executeDelegatecall(address target, bytes memory data) public {
            (bool success, ) = target.delegatecall(data); // ❌ No validation, attacker-controlled contract can be used
            require(success, "Delegatecall failed");
        }
    }
    ```
- Anyone can call `executeDelegatecall()` with a malicious contract, which will execute arbitrary code within the caller’s context.
- Can lead to theft of funds, privilege escalation, or state corruption.

- **Secure Delegatecall Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureDelegatecall {
        address public owner;
        address public trustedTarget;

        modifier onlyOwner() {
            require(msg.sender == owner, "Not authorized");
            _;
        }

        constructor(address _trustedTarget) {
            owner = msg.sender;
            trustedTarget = _trustedTarget;
        }

        function updateTrustedTarget(address _newTarget) public onlyOwner {
            require(isTrusted(_newTarget), "Untrusted target");
            trustedTarget = _newTarget;
        }

        function executeDelegatecall(bytes memory data) public onlyOwner {
            require(trustedTarget != address(0), "Invalid target");
            (bool success, ) = trustedTarget.delegatecall(data);
            require(success, "Delegatecall failed");
        }

        function isTrusted(address _target) internal pure returns (bool) {
            // Implement further checks if needed
            return _target != address(0);
        }
    }
    ```

Fixes:
- Only the contract owner can update trustedTarget.
- Validation of trusted target before executing delegatecall.
- Prevents arbitrary execution by restricting calls to trustedTarget.
