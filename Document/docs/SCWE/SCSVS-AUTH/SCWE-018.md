---
title: tx.origin を使用した認可 (Use of tx.origin for Authorization)
id: SCWE-018
alias: use-of-tx-origin-for-authorization
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [346]
status: new
---

## 関連
- **CWE-346**: Origin Validation Error  
  [https://cwe.mitre.org/data/definitions/346.html](https://cwe.mitre.org/data/definitions/346.html)

## 説明
スマートコントラクトがトランザクションの発信元をチェックし、ユーザーがアクションを実行するために認可されているかどうかを判断する際に、`tx.origin` を使用した認可はセキュリティ上の脆弱性となります。`tx.origin` はトランザクションのチェーンを通じて攻撃者によって悪用され、認可されていないユーザーがコントラクトとやり取りできるようになるため、このアプローチは欠陥があります。攻撃者は、トランザクションチェーンの別のコントラクトを利用することで、コントラクトを騙して被害者に代わってアクションを実行する可能性があります。

**主な問題**:
- `msg.sender` ではなく `tx.origin` 変数を使用することで、認可されていないトランザクションを許可します。
- フィッシング攻撃や再入攻撃に脆弱であり、攻撃者はコントラクトを使用して被害者になりすますことができます。
- ロールの管理ミスや不適切なトランザクションフロー処理。

## 対策
- **`tx.origin` ではなく `msg.sender` を使用する**: 現在の呼び出しの直接の送信者を正しく表すために、認証と認可には常に `msg.sender` に依拠します。
- **厳格なバリデーションチェック**: 認可チェックは、発信者ではなく、トランザクションの直接の送信者、つまり `msg.sender` に対して行われるようにします。

## 事例

### 脆弱なコントラクト (`tx.origin` を使用した認可)
```solidity
pragma solidity ^0.8.0;

contract Vulnerable {
    address public owner;

    constructor() public {
        owner = msg.sender;
    }

    function restrictedAction() public {
        require(tx.origin == owner, "Only the owner can perform this action");
        // Action code here
    }
}
```
In the vulnerable contract, the authorization check uses `tx.origin` to validate the caller. This is a problem because if the contract is called via another contract, `tx.origin` will return the original transaction sender (not the immediate caller), which opens up the contract to attacks such as phishing. The attacker can create a contract that interacts with the vulnerable contract on behalf of the victim.



### 修正したコントラクト (msg.sender を使用した認可)
```solidity
pragma solidity ^0.8.0;

contract Secure {
    address public owner;

    constructor() public {
        owner = msg.sender;
    }

    function restrictedAction() public {
        require(msg.sender == owner, "Only the owner can perform this action");
        // Action code here
    }
}

```
In the fixed contract, `msg.sender` is used instead of `tx.origin`. This ensures that the contract checks the immediate sender of the call, which prevents attacks where malicious contracts impersonate the victim.
