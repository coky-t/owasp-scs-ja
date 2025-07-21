## OWASP スマートコントラクトセキュリティ検証標準 日本語版

* [OWASP スマートコントラクトセキュリティ検証標準](Document/docs/SCSVS/index.md)

* [SCSVS-ARCH](Document/docs/SCSVS/05-SCSVS-ARCH.md) S1. アーキテクチャ、設計、脅威モデリング (Architecture, Design, and Threat Modeling)
  * [SCSVS-ARCH-1](Document/docs/SCSVS/controls/SCSVS-ARCH-1.md) S1.1 セキュアデザインパターン (Secure Design Patterns)
  * [SCSVS-ARCH-2](Document/docs/SCSVS/controls/SCSVS-ARCH-2.md) S1.2 プロキシパターン (Proxy Patterns)
  * [SCSVS-ARCH-3](Document/docs/SCSVS/controls/SCSVS-ARCH-3.md) S1.3 脅威モデリング (Threat Modeling)

* [SCSVS-CODE](Document/docs/SCSVS/06-SCSVS-CODE.md) S2. ポリシー、手続き、コード管理 (Policies, Procedures, and Code Management)
  * [SCSVS-CODE-1](Document/docs/SCSVS/controls/SCSVS-CODE-1.md) S2.1 開発ポリシー (Development Policies)
  * [SCSVS-CODE-2](Document/docs/SCSVS/controls/SCSVS-CODE-2.md) S2.2 コードの明確性 (Code Clarity)
  * [SCSVS-CODE-3](Document/docs/SCSVS/controls/SCSVS-CODE-3.md) S2.3 テストカバレッジ (Test Coverage)

* [SCSVS-GOV](Document/docs/SCSVS/07-SCSVS-GOV.md) S3. ビジネスロジックと経済のセキュリティ (Business Logic and Economic Security)
  * [SCSVS-GOV-1](Document/docs/SCSVS/controls/SCSVS-GOV-1.md) S3.1 経済モデル (Economic Models)
  * [SCSVS-GOV-2](Document/docs/SCSVS/controls/SCSVS-GOV-2.md) S3.2 トークノミクス (Tokenomics)
  * [SCSVS-GOV-3](Document/docs/SCSVS/controls/SCSVS-GOV-3.md) S3.3 再入可能性とロジックの欠陥の防止 (Preventing Reentrancy and Logic Flaws)

* [SCSVS-AUTH](Document/docs/SCSVS/08-SCSVS-AUTH.md) S4. アクセス制御と認証 (Access Control and Authentication)
  * [SCSVS-AUTH-1](Document/docs/SCSVS/controls/SCSVS-AUTH-1.md) S4.1 ロールベースのアクセス制御 (RBAC) (Role-Based Access Control (RBAC))
  * [SCSVS-AUTH-2](Document/docs/SCSVS/controls/SCSVS-AUTH-2.md) S4.2 認可メカニズム (Authorization Mechanisms)
  * [SCSVS-AUTH-3](Document/docs/SCSVS/controls/SCSVS-AUTH-3.md) S4.3 分散型アイデンティティ (Decentralized Identity)

* [SCSVS-COMM](Document/docs/SCSVS/09-SCSVS-COMM.md) S5. 安全なインタラクションと通信 (Secure Interactions and Communications)
  * [SCSVS-COMM-1](Document/docs/SCSVS/controls/SCSVS-COMM-1.md) S5.1 コントラクトインタラクション (Contract Interactions)
  * [SCSVS-COMM-2](Document/docs/SCSVS/controls/SCSVS-COMM-2.md) S5.2 オラクル統合 (Oracle Integrations)
  * [SCSVS-COMM-3](Document/docs/SCSVS/controls/SCSVS-COMM-3.md) S5.3 クロスチェーンインタラクション (Cross-Chain Interactions)
  * [SCSVS-COMM-4](Document/docs/SCSVS/controls/SCSVS-COMM-4.md) S5.4 ブリッジ (Bridges)

* [SCSVS-CRYPTO](Document/docs/SCSVS/10-SCSVS-CRYPTO.md) S6. 暗号化の実践 (Cryptographic Practices)
  * [SCSVS-CRYPTO-1](Document/docs/SCSVS/controls/SCSVS-CRYPTO-1.md) S6.1 鍵管理 (Key Management)
  * [SCSVS-CRYPTO-2](Document/docs/SCSVS/controls/SCSVS-CRYPTO-2.md) S6.2 署名検証 (Signature Verification)
  * [SCSVS-CRYPTO-3](Document/docs/SCSVS/controls/SCSVS-CRYPTO-3.md) S6.3 安全な乱数生成 (Secure Random Number Generation)

* [SCSVS-ORACLE](Document/docs/SCSVS/11-SCSVS-ORACLE.md) S7. 算術と論理のセキュリティ (Arithmetic and Logic Security)
  * [SCSVS-ORACLE-1](Document/docs/SCSVS/controls/SCSVS-ORACLE-1.md) S7.1 オーバーフロー/アンダーフローの防止 (Preventing Overflow/Underflow)
  * [SCSVS-ORACLE-2](Document/docs/SCSVS/controls/SCSVS-ORACLE-2.md) S7.2 算術の完全性 (Arithmetic Integrity)

* [SCSVS-BLOCK](Document/docs/SCSVS/12-SCSVS-BLOCK.md) S8. サービス拒否 (Denial of Service (DoS))
  * [SCSVS-BLOCK-1](Document/docs/SCSVS/controls/SCSVS-BLOCK-1.md) S8.1 ガス制限 (Gas Limits)
  * [SCSVS-BLOCK-2](Document/docs/SCSVS/controls/SCSVS-BLOCK-2.md) S8.2 資源枯渇に対する耐性 (Resilience Against Resource Exhaustion)

* [SCSVS-BRIDGE](Document/docs/SCSVS/13-SCSVS-BRIDGE.md) S9. ブロックチェーンデータと状態管理 (Blockchain Data and State Management)
  * [SCSVS-BRIDGE-1](Document/docs/SCSVS/controls/SCSVS-BRIDGE-1.md) S9.1 状態管理 (State Management)
  * [SCSVS-BRIDGE-2](Document/docs/SCSVS/controls/SCSVS-BRIDGE-2.md) S9.2 データプライバシー (Data Privacy)
  * [SCSVS-BRIDGE-3](Document/docs/SCSVS/controls/SCSVS-BRIDGE-3.md) S9.3 イベントログ記録 (Event Logging)
  * [SCSVS-BRIDGE-4](Document/docs/SCSVS/controls/SCSVS-BRIDGE-4.md) S9.4 分散ストレージ (Decentralized Storage)

* [SCSVS-DEFI](Document/docs/SCSVS/14-SCSVS-DEFI.md) S10. ガスの使用量、効率、制限 (Gas Usage, Efficiency, and Limitations)
  * [SCSVS-DEFI-1](Document/docs/SCSVS/controls/SCSVS-DEFI-1.md) S10.1 ガス使用の最適化 (Optimizing Gas Usage)
  * [SCSVS-DEFI-2](Document/docs/SCSVS/controls/SCSVS-DEFI-2.md) S10.2 効率的なコントラクト設計 (Efficient Contract Design)

* [SCSVS-COMP](Document/docs/SCSVS/15-SCSVS-COMP.md) S11. コンポーネント固有のセキュリティ (Component-Specific Security)
  * [SCSVS-COMP-1](Document/docs/SCSVS/controls/SCSVS-COMP-1.md) S11.1 トークン (ERC20, ERC721, ERC1155) (Tokens (ERC20, ERC721, ERC1155))
  * [SCSVS-COMP-2](Document/docs/SCSVS/controls/SCSVS-COMP-2.md) S11.2 NFT セキュリティ (NFT Security)
  * [SCSVS-COMP-3](Document/docs/SCSVS/controls/SCSVS-COMP-3.md) S11.3 保管室 (Vaults)
  * [SCSVS-COMP-4](Document/docs/SCSVS/controls/SCSVS-COMP-4.md) S11.4 流動的ステーキング (Liquid Staking)
  * [SCSVS-COMP-5](Document/docs/SCSVS/controls/SCSVS-COMP-5.md) S11.5 流動性プール (AMM) (Liquidity Pools (AMMs))
  * [SCSVS-COMP-6](Document/docs/SCSVS/controls/SCSVS-COMP-6.md) S11.6 Uniswap V4 フック (Uniswap V4 Hook)
