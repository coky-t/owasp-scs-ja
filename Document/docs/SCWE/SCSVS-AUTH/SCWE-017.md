---
title: 特権ロールの管理ミス (Privileged Role Mismanagement)
id: SCWE-017
alias: privileged-role-mismanagement
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [269]
status: new
---

## 関連
- CWE-269: Improper Privilege Management  
  [https://cwe.mitre.org/data/definitions/269.html](https://cwe.mitre.org/data/definitions/269.html)

## 説明
特権トールの管理ミスは、スマートコントラクトがロールやパーミッションを誤って割り当て、特定のユーザーに過剰な権限を付与した場合に発生します。これはユーザーが意図した機能に必要なものを超えてパーミッションを取得し、権限昇格や不正なアクションをもたらす可能性があります。スマートコントラクトのコンテキストでは、不適切な権限管理は、不正なコントラクト変更、悪意のあるインタラクション、資金の喪失などの重大な脆弱性につながる可能性があります。

よくある原因には以下があります。
- 特権機能にアクセスしようとするユーザーに対する不十分なチェック。
- バリデーションなしで動的に割り当てられるロール。
- 機密性の高いコントラクト機能に対する不十分なアクセス制御メカニズム。

## 対策
- **ロールベースのアクセス制御 (RBAC):** 修飾子を使用して厳格なロールチェックを実装し、ユーザーが割り当てられたロールに対応するアクションのみを実行できるようにします。
- **最小権限の原則:** 各ロールの権限をタスクの実行に必要な最小限のみに制限します。
- **信頼できるソースまたは不変なソースの使用:** 特権ロールが、認可されていないユーザーや攻撃によって、恣意的に変更されないようにします。
- **定期的なレビューと監査:** ロールとパーミッションを定期的にレビューし、それらが適切に割り当てられ、維持されていることを確認します。

## 事例

### 特権ロールの管理ミスの例

```solidity
pragma solidity ^0.8.0;

contract PrivilegedRoleMismanagement {
    address public admin;
    uint public balance;

    function setAdmin(address _admin) public {
        admin = _admin;
    }

    function withdraw(uint amount) public {
        require(msg.sender == admin, "Only admin can withdraw");
        balance -= amount;
    }
}
```

上記の例では、`admin` ロールを割り当てるアドレスに対するチェックがなく、悪意のある人物を含む誰でも変更できる可能性があります。

### 修正した特権ロール管理
```solidity
pragma solidity ^0.8.0;

contract FixedRoleManagement {
    address public owner;
    uint public balance;

    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }

    constructor() public {
        owner = msg.sender;
    }

    function setOwner(address _owner) public onlyOwner {
        owner = _owner;
    }

    function withdraw(uint amount) public onlyOwner {
        balance -= amount;
    }
}
```
修正版では、`onlyOwner` 修飾子を使用して、所有権の譲渡や資金の引き出しなどの機密性の高いアクションを所有者のみが実行できるようにします。これは、コントラクトレベルでアクセス制御を実行することで、権限管理の不備によるリスクを緩和できます。
