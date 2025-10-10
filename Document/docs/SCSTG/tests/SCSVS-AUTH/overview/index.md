---
title: アクセス制御と認証の脆弱性 (Access Control and Authentication Vulnerabilties)
---
### **説明**

不適切なアクセス制御はスマートコントラクトにおける重大なセキュリティ脆弱性であり、認可されていないユーザーが機密性の高い機能やデータにアクセスしたり、変更できる場合に発生します。この問題は一般的に、コードがユーザーのパーミッションに基づいた厳格なアクセス制御を適用していない場合に発生します。

アクセス制御の脆弱性は、以下のような、ガバナンスや重要な操作に関わるシナリオにおいて特に深刻です。

- トークンの発行
- 提案への投票
- 資金の引き落とし
- コントラクトの一時停止またはアップグレード
- コントラクトの所有者の変更

### **例: 適切なアクセス制御のないコード**


```solidity
function burn(address account, uint256 amount) public 
{
    // No access control is implemented for the burn function
    _burn(account, amount); 
}
```
### **影響**

- 攻撃者はコントラクト内の重要な機能やデータに不正アクセスし、その完全性とセキュリティを侵害する可能性があります。
- 脆弱性はコントラクトで管理されている資金や資産の盗難につながり、ユーザーや利害関係者に重大な経済的損害をもたらす可能性があります。


### **対策**


- 初期化関数は認可されたエンティティによってのみ一度だけ呼び出されるようにします。
- コントラクトにおいて Ownable や RBAC (Role-Based Access Control) などの確立されたアクセス制御パターンを使用してパーミッションを管理し、認可されたユーザーのみが特定の機能にアクセスできるようにします。これは onlyOwner などの適切なアクセス制御修飾子や、機密性の高い機能へのカスタムロールを追加することで実現できます。

### 不適切なアクセス制御攻撃の被害を受けたスマートコントラクトの事例:

- [HospoWise Hack Analysis](https://blog.solidityscan.com/access-control-vulnerabilities-in-smart-contracts-a31757f5d707)
- [LAND NFT Hack Analysis](https://blog.solidityscan.com/land-hack-analysis-missing-access-control-66fb9555a3e3)
