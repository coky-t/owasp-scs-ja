---
title: ハードコードされた定数 (Hardcoded Constants)
id: SCWE-008
alias: hardcoded-constants
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-2]
  cwe: [547]
status: new
---

## 関連
- CWE-547: Use of Hard-coded, Security-relevant Constants  
  [https://cwe.mitre.org/data/definitions/547.html](https://cwe.mitre.org/data/definitions/547.html)

## 説明
ハードコードされた定数は、コードに直接埋め込まれ、コード自体を変更しなければ簡単に変更できない値を指します。これらの定数は、外部要素やニーズの変化に基づいて変更の対象となる可能性のある重要なパラメータ、アドレス、設定を含むことがあります。これらの値をコードにハードコードすると、いくつかの問題をもたらします。

- **柔軟性の欠如**: コントラクトがデプロイされると、これらのハードコードされた値は新しいバージョンをデプロイしない限り変更できず、非効率性と適応性低下につながります。
- **セキュリティリスク**: ハードコードされた値は、適切に保護されていない場合、機密情報をさらしたり、脆弱性を生み出す可能性があります。
- **アップグレードの課題**: ハードコードされた定数を持つコントラクトは、コストのかかる再デプロイメントを必要とせずに新しい機能やパラメータをサポートするように簡単に進化することができません。

## 対策
- **定数ではなく変数を使用する**: 値をハードコードするのではなく、管理アクションを通じて更新できる変数として定義します。
- **アップグレード可能なコントラクトパターンを実装する**: プロキシコントラクトや、アップグレードをサポートするその他のパターンを使用して、定数を変更する柔軟性を許容します。
- **外部構成**: 新しいコントラクトバージョンをデプロイする必要なく更新できる構成値には、オフチェーンストレージを使用します。

## 事例

### ハードコードされた定数のあるコントラクト

```solidity
pragma solidity ^0.8.0;

contract HardcodedConstants {
    address public owner = 0x1234567890abcdef1234567890abcdef12345678; // Hardcoded address
    uint public maxSupply = 1000000; // Hardcoded supply limit

    function setOwner(address newOwner) public {
        owner = newOwner;
    }

    function setMaxSupply(uint newMaxSupply) public {
        maxSupply = newMaxSupply;
    }
}
```
この例では、`owner` アドレスと `maxSupply` はハードコードされた値であり、コントラクトを再デプロイしない限り変更できません。これは柔軟性を低下し、潜在的なセキュリティリスクを生み出します。


### 外部設定に改善されたコントラクト
```solidity
pragma solidity ^0.8.0;

contract ConfigurableContract {
    address public owner;
    uint public maxSupply;

    constructor(address initialOwner, uint initialMaxSupply) {
        owner = initialOwner;
        maxSupply = initialMaxSupply;
    }

    function setOwner(address newOwner) public {
        owner = newOwner;
    }

    function setMaxSupply(uint newMaxSupply) public {
        maxSupply = newMaxSupply;
    }
}
```

この改善された例では、`owner` アドレスと `maxSupply` はコンストラクタを通じて構成可能であり、再デプロイメントを必要とせずにより柔軟性を向上します。
