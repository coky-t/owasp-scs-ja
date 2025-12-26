---
title: フロントランニングに対する不十分な保護 (Insufficient Protection Against Front-Running)
id: SCWE-037
alias: insufficient-protection-against-front-running
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
フロントランニングに対する不十分な保護は悪意のある人物がトランザクションの順序を悪用して利益を得ることを可能にする脆弱性を指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **コミットリビールスキームを使用する:** コミットリビールメカニズムを実装して、トランザクションの詳細が確定するまでそれを隠します。
- **遅延を追加する:** 重要な操作に時間遅延を導入して、フロントランニングのリスクを軽減します。
- **徹底的にテストする:** 広範なテストを実施して、フロントランニング保護が効果的であることを確認します。

## 事例
- **フロントランニングに脆弱**
    ```solidity
    pragma solidity ^0.8.0;

    interface IERC20 {
        function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
        function balanceOf(address account) external view returns (uint256);
    }

    contract FrontRunningVulnerable {
        IERC20 public token;
        uint256 public price = 1 ether; // 1 token = 1 ETH

        constructor(address _token) {
            token = IERC20(_token);
        }

        function buyTokens(uint256 amount) public payable {
            require(msg.value >= amount * price, "Insufficient ETH");
            token.transferFrom(address(this), msg.sender, amount);
        }
    }
    ```
なぜこれが脆弱なのか？
- 攻撃者はメモリプールのトランザクションを参照し、まず購入トランザクションを実行して価格を引き上げます。
- 被害者のトランザクションはより高い価格で実行するか、スリッページにより失敗します。
- 攻撃者は、サンドイッチ攻撃を悪用して、利益を得て売却します。

- **フロントランニングに対する保護あり - トレード意図を隠すためのコミットリビール**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureTrade {
        mapping(address => bytes32) public commitments;
        uint256 public price = 1 ether; // 1 token = 1 ETH

        function commitTrade(bytes32 hash) public {
            commitments[msg.sender] = hash;
        }

        function executeTrade(uint256 amount, bytes32 secret) public payable {
            require(commitments[msg.sender] == keccak256(abi.encodePacked(amount, secret)), "Invalid commitment");
            require(msg.value >= amount * price, "Insufficient ETH");
            
            commitments[msg.sender] = 0; // Prevent reusing commitment
            // Execute trade after revealing the commitment
        }
    }
    ```
なぜこれが優れているのか？
- トレーダーは金額を公開する前にオフチェーンでトレードをコミットし、メモリプールのスナイピングを防ぎます。
- 攻撃者は公開されるまで金額を分からないため、トランザクションはフロントランできません。
---
