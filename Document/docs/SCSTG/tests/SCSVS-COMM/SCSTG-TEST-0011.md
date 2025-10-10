---
id: SCSTG-TEST-0011
scsvs_cg_id:
- SCSVS-COMM
scsvs_scg_id:
- SCSVS-COMM-1
platform: []
title: コントラクトインタラクションのテスト (Test Contract Interactions)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0011
---

### **説明**
スマートコントラクトにおける安全なインタラクションと通信は、不正アクセス、不正操作、攻撃から保護するために不可欠です。これは、コントラクトが外部コントラクトと安全にやり取りすること、オラクルとの統合を安全に管理すること、クロスチェーンおよびブリッジ操作に安全な方法を使用することを確保することを含みます。これらのインタラクションに関するベストプラクティスに従わないと、コントラクトを再入可能性、フロントランニング、不正なデータフィードなどの脆弱性にさらす可能性があります。このテストは、スマートコントラクトが安全なインタラクションと通信を実行し、一般的な攻撃ベクトルからシステムを保護することを検証することに重点を置いています。

---

### **テスト 1: 安全なコントラクトインタラクションを確認する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.5.0;

contract VulnerableContract {
    address public trustedAddress;

    constructor(address _trustedAddress) public {
        trustedAddress = _trustedAddress;
    }

    function callExternalContract(address _to, uint256 _value) public {
        _to.call{value: _value}(""); // Vulnerable to reentrancy or other attacks
    }
}
```

#### **なぜ脆弱なのか**
- このコントラクトは外部呼び出しをするために `.call` を使用していますが、これは一般的に安全ではなく、再入攻撃の影響を受けやすくなります。
- この関数は、誰でも呼び出しをトリガーして資金を送金できるため、コントラクトが潜在的な攻撃にさらされます。


#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract SecureContract {
    address public trustedAddress;

    constructor(address _trustedAddress) {
        trustedAddress = _trustedAddress;
    }

    function callExternalContract(address _to, uint256 _value) public {
        require(_to != address(0), "Invalid address");
        (bool success, ) = _to.call{value: _value}("");
        require(success, "External call failed");
    }
}
```
#### **チェック方法**
- **コードレビュー:** コントラクト内の外部呼び出しを探し、適切な場所で `transfer` や `send` などの安全なメソッドを使用しているか、再入保護を実装していることを確認します。
- **静的解析:** SolidityScan, MythX, Slither などの静的解析ツールを使用して、コード内の安全でない呼び出しを検出します。
- **動的テスト:** 脆弱なコントラクトを呼び出して資金を流出することを試みるコントラクトをデプロイすることで、再入攻撃をシミュレートします。
