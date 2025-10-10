---
id: SCSTG-TEST-0012
scsvs_cg_id:
- SCSVS-COMP
scsvs_scg_id:
- SCSVS-COMP-1
platform: []
title: トークン実装のテスト (ERC20, ERC721, ERC1155) (Test Token Implementations (ERC20, ERC721, ERC1155))
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0012
---

### **説明**
コンポーネント固有のセキュリティは、分散型アプリケーション (dApp) やスマートコントラクトエコシステムのそれぞれのコンポーネントが安全に実装されていることを確保することに重点を置いています。これは、トークン標準 (ERC20, ERC721, ERC1155)、NFT、Vault、流動性プール、その他のコンポーネントなどの幅広い領域を含みます。これらのコンポーネントを適切に保護することは、資金の盗難、損失、悪用につながる脆弱性を避けるために不可欠です。このテストは、コントロールにリストされているコンポーネントのセキュリティ上の考慮事項を検証することに重点を置き、それぞれのものが安全に実装され、ユーザーと資産を保護するための適切なメカニズムを備えていることを確認します。

---

### **テスト: 適切なトークン実装 (ERC20, ERC721, ERC1155) を確認する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.5.0;

contract SimpleERC20 {
    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;
    
    string public name = "Simple Token";
    string public symbol = "STK";
    uint8 public decimals = 18;
    uint256 public totalSupply;
    
    // Unchecked approve function (vulnerable to approval race condition)
    function approve(address spender, uint256 amount) public returns (bool) {
        allowance[msg.sender][spender] = amount;
        return true;
    }
}
```

#### **なぜ脆弱なのか**
- `approve` 関数は、新しい allowance を設定する前に現在の allowance に対するチェックを含んでいないため、「承認競合状態」をもたらす可能性があります。これは、攻撃者が allowance メカニズムを回避し、意図したよりも多くのトークンを転送できる脆弱性を生じる可能性があります。
- この問題は ERC20 トークン実装における既知の脆弱性です。


#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract SafeERC20 {
    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;
    
    string public name = "Safe Token";
    string public symbol = "STK";
    uint8 public decimals = 18;
    uint256 public totalSupply;
    
    // Secure approve function to prevent race condition
    function approve(address spender, uint256 amount) public returns (bool) {
        require(amount == 0 || allowance[msg.sender][spender] == 0, "Approve: non-zero allowance");
        allowance[msg.sender][spender] = amount;
        return true;
    }
}
```

### **チェック方法**
- **コードレビュー:** トークンコントラクト内の `approve` 関数を探し、競合状態を防ぐために必要なチェックを含むことを確認します。安全なアプローチは、新しい値に更新する前にまず allowance をゼロに設定するか、リセットする場合はゼロであることを必須とすることです。
- **静的解析:** SolidityScan, MythX, Slither などのツールを使用して「承認競合状態」をチェックし、コントラクトがこの脆弱性を許容していないことを確認します。
- **動的テスト:** `approve` を呼び出す前に allowance にゼロ以外の値を設定するエッジケースでトークン転送機能をテストします。それが正しく機能し、不正な転送ができないことを検証します。
