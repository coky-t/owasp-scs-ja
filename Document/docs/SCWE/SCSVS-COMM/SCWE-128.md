---
title: 安全でないマルチコールコンテキスト転送 (Insecure Multicall Context Forwarding)
id: SCWE-128
alias: insecure-multicall-context
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [841]
status: new
---

## 関連
- CWE-841: Improper Enforcement of Behavioral Workflow  
  [https://cwe.mitre.org/data/definitions/841.html](https://cwe.mitre.org/data/definitions/841.html)

## 説明
リエントランシーやコンテキストの変化に対するガードなしで呼び出しを転送するマルチコール形式のアグリゲーターは、攻撃者に、単一のトランザクション内でアクションを順序入れ替え (例: 預け入れ後の引き落とし) されたり、内部呼び出しが `tx.origin` やキャッシュされた送信者状態を使用する場合に `msg.sender` を偽装します。

## 対策
- マルチコールのエントリポイントにはリエントランシーガードを適用します。
- 呼び出しをまたいで `msg.sender` をキャッシュすることを避け、送信者やコンテキストを明示的に内部関数へ渡します。
- 呼び出し可能なセレクタやターゲットを制限するか、許可リストを適用します。

## 事例

### 脆弱
```solidity
function multicall(bytes[] calldata data) external {
    for (uint i; i < data.length; i++) {
        (bool ok, ) = address(this).delegatecall(data[i]);
        require(ok, "fail");
    }
}
```

### 修正済み
```solidity
function multicall(bytes[] calldata data) external nonReentrant {
    for (uint i; i < data.length; i++) {
        _dispatch(msg.sender, data[i]); // explicit context, no delegatecall loops
    }
}
```
