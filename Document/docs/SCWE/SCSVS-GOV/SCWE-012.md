---
title: マルチシグガバナンスの欠如 (Lack of Multisig Governance)
id: SCWE-012
alias: lack-of-multisig-governance
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-1]
  cwe: [285]
status: new
---

## 関連
- CWE-285: Improper Authorization  
  [https://cwe.mitre.org/data/definitions/285.html](https://cwe.mitre.org/data/definitions/285.html)

## 説明
マルチシグガバナンスの欠如は、アップグレード、資金の引き落とし、パラメータの変更といった、重要なスマートコントラクト機能が単一のエンティティによって制御されている場合に発生します。これは **単一障害点** を作り出し、**侵害、内部関係者による悪用、不正アクセス** のリスクを高めます。マルチシグガバナンスなしでは、単一の管理者の秘密鍵が侵害された場合、攻撃者や悪意のある人物が特権機能を容易に悪用できます。

マルチシグガバナンスの欠如に伴う主なリスク:
- **集中化リスク**: 単一エンティティが主要なコントラクトパラメータを制御および変更できます。
- **単一障害点**: 所有者の秘密鍵の喪失または侵害は壊滅的な結果をもたらす可能性があります。
- **不正アクセス**: 攻撃者が秘密鍵の制御を獲得すると、承認なしで特権機能を実行できます。
- **説明責任の欠如**: 意思決定は一方的に行われ、透明性とセキュリティを低下します。

## 対策
- **マルチシグウォレットを実装する**: マルチシグスキーム (Gnosis Safe など) を使用して、重要なトランザクションには複数の署名者を要求します。
- **ロールベースのアクセス制御 (RBAC)**: 異なる権限を持つ複数のロールを割り当て、集中管理を防止します。
- **重要な機能のタイムロック**: 特権アクションに遅延を持ち込み、必要に応じてコミュニティの介入のための時間を確保します。
- **オンチェーンガバナンスメカニズム**: 適用できる場合には **DAO ベースのガバナンス** を使用して意思決定を分散化します。

## 事例

### マルチシグガバナンスのないコントラクトの例 (集中管理の所有者)

```solidity
pragma solidity ^0.8.0;

contract CentralizedGovernance {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function upgradeContract(address newContract) public {
        require(msg.sender == owner, "Only owner can upgrade");
        // ❌ Only a single owner can perform critical actions
    }

    function withdrawFunds(address payable recipient, uint256 amount) public {
        require(msg.sender == owner, "Only owner can withdraw funds");
        recipient.transfer(amount); // ❌ No multisig verification
    }
}
```

- In this example, all governance actions depend on a single owner, making it a high-risk design.

### マルチシグガバナンスを使用するようにリファクタリングしたもの

```solidity
pragma solidity ^0.8.0;

interface IMultiSig {
    function submitTransaction(address destination, uint256 value, bytes calldata data) external;
}

contract SecureGovernance {
    IMultiSig public multisigWallet;

    constructor(address _multisigWallet) {
        multisigWallet = IMultiSig(_multisigWallet);
    }

    function upgradeContract(address newContract) public {
        bytes memory data = abi.encodeWithSignature("upgradeTo(address)", newContract);
        multisigWallet.submitTransaction(address(this), 0, data); // ✅ Requires multisig approval
    }

    function withdrawFunds(address payable recipient, uint256 amount) public {
        bytes memory data = abi.encodeWithSignature("transfer(address,uint256)", recipient, amount);
        multisigWallet.submitTransaction(address(this), 0, data); // ✅ Multisig verification for withdrawals
    }
}
```

- This improved version delegates authority to a multisig wallet, requiring multiple approvals before executing critical actions.
