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
- **Validate targets:** Ensure the target contract is trusted and secure.
- **Use secure libraries:** Leverage well-audited libraries for external calls.
- **Test thoroughly:** Conduct extensive testing to ensure external calls are secure.

## 事例
- **Insecure External Call**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureExternalCall {
        function callExternal(address target, bytes memory data) public {
            (bool success, ) = target.call(data); // No validation
            require(success, "Call failed");
        }
    }
    ```

- **Secure External Call**
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
Why is this secure?
- No arbitrary data input—Only predefined `function executeAction(uint256)` can be called.
- Access control added—Only the owner can execute external calls.
- Interface-based call—Avoids raw `.call()`, reducing attack vectors.
- Immutable trusted address—Prevents runtime modifications to the target contract.

---
