---
title: Selfdestruct の安全でない使用 (Insecure Use of Selfdestruct)
id: SCWE-038
alias: insecure-use-of-selfdestruct
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [732]
status: new
---

## 関連
- CWE-732: Incorrect Permission Assignment for Critical Resource  
  [CWE-732 Link](https://cwe.mitre.org/data/definitions/732.html)

## 説明
selfdestruct の安全でない使用は `selfdestruct` 関数が適切な安全策なしで使用される場合に生じる脆弱性を指します。これは以下につながる可能性があります。
- コントラクトの不正な破棄。
- 資金やデータの喪失。
- コントラクトロジックの脆弱性の悪用。

## 対策
- **アクセスを制限する:** 認可されたアドレスのみが `selfdestruct` を呼び出すことができるようにします。注: `selfdestruct` は EIP-6049 (Solidity 0.8.24+) により非推奨です。可能な場合はマイグレーションパターンを優先します。
- **サーキットブレーカーを実装する:** 疑わしいアクティビティの場合に操作を停止するメカニズムを追加します。
- **徹底的にテストする:** 広範なテストを実施して、`selfdestruct` が安全に使用されることを確認します。

## 事例
- **安全でない Selfdestruct の使用**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureSelfdestruct {
        function destroy() public {
            selfdestruct(payable(msg.sender)); // No access control
        }
    }
    ```

- **安全な Selfdestruct の使用**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureSelfdestruct {
        address public admin;

        constructor(address _admin) {
            admin = _admin;
        }

        modifier onlyAdmin() {
            require(msg.sender == admin, "Unauthorized");
            _;
        }

        function destroy() public onlyAdmin {
            selfdestruct(payable(admin)); // Restricted to admin
        }
    }
    ```

---
