---
id: SCSTG-TEST-0007
scsvs_cg_id:
- SCSVS-ARCH
scsvs_scg_id:
- SCSVS-ARCH-2
platform: []
title: モジュール性とアップグレード可能性 (Modularity and Upgradability)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0007
---

### **説明**
モジュール性とアップグレード可能性は、スマートコントラクトの長期的なセキュリティと保守性にとって不可欠な原則です。モジュール性が不十分だと、重要なロジックとストレージが一体化したモノリシックな設計になりがちで、システムのアップグレード、監査、拡張が困難になります。制御されたアップグレードメカニズムがなければ、攻撃者はアップグレードプロセスの弱点を悪用し、不正なコントラクトの変更やセキュリティ欠陥の導入につながる可能性があります。このような脆弱性を回避するには、モジュールが明確に分離され、適切に構造化されたコントラクトと、安全なアップグレードプロセスを確保することが不可欠です。

---

### **テスト 1: ロジックと状態の適切な分離を確認する**

#### 脆弱なコード:
```solidity
contract Monolithic {
    uint256 public data;
    address public admin;

    function updateData(uint256 _data) public {
        require(msg.sender == admin, "Unauthorized");
        data = _data;
    }

    function upgrade(address newAdmin) public {
        require(msg.sender == admin, "Unauthorized");
        admin = newAdmin;
    }
}
```

#### **なぜ脆弱なのか**
- モノリシック設計ではロジックと状態を単一のコントラクトに一体化するため、アップグレートにリスクが伴います。
- ストレージやロジックを変更すると、既存のデータを不注意で破損する可能性があります。
- 分離の欠如のため、ロジック内のバグや脆弱性の切り分けが困難になります。

#### 修正されたコード:

```solidity
contract Logic {
    address public admin;
    Storage public storageContract;

    constructor(address _storageContract) {
        admin = msg.sender;
        storageContract = Storage(_storageContract);
    }

    function updateData(uint256 _data) public {
        require(msg.sender == admin, "Unauthorized");
        storageContract.setData(_data);
    }
}

contract Storage {
    uint256 public data;

    function setData(uint256 _data) public {
        data = _data;
    }
}
```
#### **チェック方法**
- コードレビュー: ロジックとストレージが別々のコントラクトに分離していることを検証します。
- ストレージ解析: コントラクトロジックをアップグレードしてもストレージレイアウトが維持されること、およびデータ破損が発生しないことを確認します。

### **テスト 2: 安全で制御されたアップデートメカニズムを検証する**

#### 脆弱なコード:

```solidity
contract Proxy {
    address public implementation;

    function upgrade(address newImplementation) public {
        implementation = newImplementation;
    }
}
```

#### **なぜ脆弱なのか**
- アップグレード関数はアクセス制御で保護されていないため、どんなユーザーでも実装コントラクトを置き換えることができます。これは悪意のある行為者がコントラクト機能を乗っ取る可能性を高めます。

#### 修正されたコード:
```solidity
contract SecureProxy {
    address public implementation;
    address public admin;

    modifier onlyAdmin() {
        require(msg.sender == admin, "Unauthorized");
        _;
    }

    function upgrade(address newImplementation) public onlyAdmin {
        implementation = newImplementation;
    }
}

```

#### **チェック方法**
- コードレビュー: アップグレード関数がアクセス制御メカニズムで保護されていることを確認します (例: 管理者のみがアップグレードできます)。
- 動的テスト: 不正なアップグレードの実行を試行し、アクセス制御が意図したとおりに機能することを検証します。
