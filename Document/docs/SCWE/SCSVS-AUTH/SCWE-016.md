---
title: 不十分な認可チェック (Insufficient Authorization Checks)
id: SCWE-016
alias: insufficient-authorization-checks
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [862]
status: new
---

## 関連
- CWE-862: Missing Authorization  
  [https://cwe.mitre.org/data/definitions/862.html](https://cwe.mitre.org/data/definitions/862.html)

## 説明
不十分な認可はユーザーが特定のアクションを実行するための適切なパーミッションを持っているかどうかをコントラクトが検証しない場合に発生します。この脆弱性は認可チェックが欠落しているか適切に実装されていない場合に発生し、認可されていないユーザーが、アクセスできないはずの機能にアクセスできるようになります。

不十分な認可のよくある原因には以下があります。
- アクセス制御修飾子の欠落、または設定の誤り。
- 機密機能へのアクセスを許可する前にロールを検証していない。
- ユーザーパーミッションの不適切な処理。権限昇格につながる可能性があります。

## 対策
- **ロールベースのアクセス制御 (RBAC) を実装する:** `admin`, `user` などのロールを定義し、それに応じたパーミッションを割り当て、認可したユーザーのみが制限された機能を呼び出すことができるようにします。
- **アクセス制御修飾子を使用する:** アクセスを制限するための適切な修飾子 (`onlyOwner`, `onlyAdmin` など) で機密機能を保護します。
- **最小権限の原則:** 各ユーザーがロールを実行するために必要な最小限のアクションのみにアクセスできるようにします。
- **徹底的なテスト:** 認可の脆弱性についてテストして、制限されたすべての機能が適切に保護されていることを確認します。

## 事例

### 不十分な認可の例

```solidity
pragma solidity ^0.8.0;

contract InsufficientAuthorization {
    uint public balance;

    function withdraw(uint amount) public {
        // No checks for user roles, any address can withdraw funds
        balance -= amount;
    }
}
```

### 修正した認可の例
```solidity
pragma solidity ^0.8.0;

contract FixedAuthorization {
    uint public balance;
    address public owner;

    constructor() public {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }

    function withdraw(uint amount) public onlyOwner {
        balance -= amount;
    }
}

```
In the fixed example, the `onlyOwner` modifier ensures that only the contract owner can withdraw funds, thus preventing unauthorized users from performing sensitive operations like withdrawing assets.
