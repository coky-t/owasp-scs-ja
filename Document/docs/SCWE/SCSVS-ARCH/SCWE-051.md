---
title: コントラクトデプロイメントにおける CREATE2 の不適切な使用 (Improper Use of CREATE2 for Contract Deployment)
id: SCWE-051
alias: improper-create2-deployment
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [706]
status: new
---

## 関連
- **CWE-706: Use of Incorrectly-Resolved Name or Reference**  
  [https://cwe.mitre.org/data/definitions/706.html](https://cwe.mitre.org/data/definitions/706.html)  

## 説明
`CREATE2` オペコードは決定論的なコントラクトのデプロイメントを可能にします。つまり、デプロイメント前にコントラクトのアドレスが事前に計算できるということです。しかし、ソルトパラメータ、コンストラクタ引数、コントラクトバイトコードの不適切な処理は、アドレス予測可能性、再デプロイメント攻撃、悪意のあるコントラクト代替などの脆弱性につながる可能性があります。攻撃者がソルトまたはコードに影響を及ぼすことができる場合、正当なものより前に既知のアドレスにコントラクトをデプロイする可能性があり、セキュリティリスクにつながります。

## 対策
`CREATE2` の不正使用を防ぐには:
- 予測不可能で一意のソルト値を使用します (例: ノンス、送信元アドレス、ランダム性を組み込みます)。
- 再デプロイメント攻撃を防ぐために、デプロイしたコントラクトロジックが一貫性を維持していることを確保します。
- 意図しないアドレス衝突を防ぐために、重要なコントラクトパラメータをソルトにハッシュ化します。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Factory {
    function deploy(bytes32 salt, bytes memory bytecode) public {
        address deployed;
        assembly {
            deployed := create2(0, add(bytecode, 0x20), mload(bytecode), salt)
        }
    }
}
```

**なぜこれが脆弱なのか？**

- `salt` が予測可能な場合 (例: ユーザー提供の値や静的な値)、攻撃者は事前に計算してデプロイメントをフロントランできます。
- 同じコントラクトアドレスが異なるバイトコードをデプロイすることにより再使用され、固定アドレスのロジック変更につながる可能性があります。
- バリデーションではデプロイされたコントラクトが安全または期待どおりであることを確保できません。

### 修正したコントラクトの例

```solidity
pragma solidity ^0.8.0;

contract SecureFactory {
    function deploy(bytes32 salt, bytes memory bytecode) public returns (address) {
        require(bytecode.length > 0, "Bytecode cannot be empty");

        // Ensure the salt includes sender information to prevent front-running
        bytes32 secureSalt = keccak256(abi.encodePacked(msg.sender, salt));

        address deployed;
        assembly {
            deployed := create2(0, add(bytecode, 0x20), mload(bytecode), secureSalt)
        }

        require(deployed != address(0), "Deployment failed");
        return deployed;
    }
}
```

**なぜこれが安全なのか？**
- `keccak256(abi.encodePacked(msg.sender, salt))` を使用して、送信者ごとにソルトを一意にしています。
- デプロイ前にコントラクトバイトコードが空ではないことを確認しています。
- デプロイされたコントラクトがゼロでないことを検証し、デプロイメントの成功を確保します。

**CREATE2 デプロイメントを保護することで、開発者は予測可能なコントラクトアドレス、フロントランニングリスク、コントラクト置換の脆弱性を防止できます。**
