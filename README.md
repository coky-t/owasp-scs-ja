# OWASP Smart Contract Security ja

This is the unofficial Japanese translation of the [OWASP Smart Contract Security](https://github.com/OWASP/owasp-scs).

- Document Repository - <https://github.com/coky-t/owasp-scs-ja>

### Originator

- Project Site - <https://scs.owasp.org/>
- Project Repository - <https://github.com/OWASP/owasp-scs>

## OWASP スマートコントラクトセキュリティ 日本語版

## OWASP スマートコントラクトセキュリティ検証標準 日本語版

* [OWASP スマートコントラクトセキュリティ検証標準](Document/docs/SCSVS/index.md)
* [SCSVS-ARCH S1. アーキテクチャ、設計、脅威モデリング (Architecture, Design, and Threat Modeling)](Document/docs/SCSVS/05-SCSVS-ARCH.md)
  * [SCSVS-ARCH-1 S1.1 セキュアデザインパターン (Secure Design Patterns)](Document/docs/SCSVS/controls/SCSVS-ARCH-1.md)
  * [SCSVS-ARCH-2 S1.2 プロキシパターン (Proxy Patterns)](Document/docs/SCSVS/controls/SCSVS-ARCH-2.md)
  * [SCSVS-ARCH-3 S1.3 脅威モデリング (Threat Modeling)](Document/docs/SCSVS/controls/SCSVS-ARCH-3.md)
* [SCSVS-CODE S2. ポリシー、手続き、コード管理 (Policies, Procedures, and Code Management)](Document/docs/SCSVS/06-SCSVS-CODE.md)
  * [SCSVS-CODE-1 S2.1 開発ポリシー (Development Policies)](Document/docs/SCSVS/controls/SCSVS-CODE-1.md)
  * [SCSVS-CODE-2 S2.2 コードの明確性 (Code Clarity)](Document/docs/SCSVS/controls/SCSVS-CODE-2.md)
  * [SCSVS-CODE-3 S2.3 テストカバレッジ (Test Coverage)](Document/docs/SCSVS/controls/SCSVS-CODE-3.md)
* [SCSVS-GOV S3. ビジネスロジックと経済のセキュリティ (Business Logic and Economic Security)](Document/docs/SCSVS/07-SCSVS-GOV.md)
  * [SCSVS-GOV-1 S3.1 経済モデル (Economic Models)](Document/docs/SCSVS/controls/SCSVS-GOV-1.md)
  * [SCSVS-GOV-2 S3.2 トークノミクス (Tokenomics)](Document/docs/SCSVS/controls/SCSVS-GOV-2.md)
  * [SCSVS-GOV-3 S3.3 再入可能性とロジックの欠陥の防止 (Preventing Reentrancy and Logic Flaws)](Document/docs/SCSVS/controls/SCSVS-GOV-3.md)
* [SCSVS-AUTH S4. アクセス制御と認証 (Access Control and Authentication)](Document/docs/SCSVS/08-SCSVS-AUTH.md)
  * [SCSVS-AUTH-1 S4.1 ロールベースのアクセス制御 (RBAC) (Role-Based Access Control (RBAC))](Document/docs/SCSVS/controls/SCSVS-AUTH-1.md)
  * [SCSVS-AUTH-2 S4.2 認可メカニズム (Authorization Mechanisms)](Document/docs/SCSVS/controls/SCSVS-AUTH-2.md)
  * [SCSVS-AUTH-3 S4.3 分散型アイデンティティ (Decentralized Identity)](Document/docs/SCSVS/controls/SCSVS-AUTH-3.md)
* [SCSVS-COMM S5. 安全なインタラクションと通信 (Secure Interactions and Communications)](Document/docs/SCSVS/09-SCSVS-COMM.md)
  * [SCSVS-COMM-1 S5.1 コントラクトインタラクション (Contract Interactions)](Document/docs/SCSVS/controls/SCSVS-COMM-1.md)
  * [SCSVS-COMM-2 S5.2 オラクル統合 (Oracle Integrations)](Document/docs/SCSVS/controls/SCSVS-COMM-2.md)
  * [SCSVS-COMM-3 S5.3 クロスチェーンインタラクション (Cross-Chain Interactions)](Document/docs/SCSVS/controls/SCSVS-COMM-3.md)
  * [SCSVS-COMM-4 S5.4 ブリッジ (Bridges)](Document/docs/SCSVS/controls/SCSVS-COMM-4.md)
* [SCSVS-CRYPTO S6. 暗号化の実践 (Cryptographic Practices)](Document/docs/SCSVS/10-SCSVS-CRYPTO.md)
  * [SCSVS-CRYPTO-1 S6.1 鍵管理 (Key Management)](Document/docs/SCSVS/controls/SCSVS-CRYPTO-1.md)
  * [SCSVS-CRYPTO-2 S6.2 署名検証 (Signature Verification)](Document/docs/SCSVS/controls/SCSVS-CRYPTO-2.md)
  * [SCSVS-CRYPTO-3 S6.3 安全な乱数生成 (Secure Random Number Generation)](Document/docs/SCSVS/controls/SCSVS-CRYPTO-3.md)
* [SCSVS-ORACLE S7. 算術と論理のセキュリティ (Arithmetic and Logic Security)](Document/docs/SCSVS/11-SCSVS-ORACLE.md)
  * [SCSVS-ORACLE-1 S7.1 オーバーフロー/アンダーフローの防止 (Preventing Overflow/Underflow)](Document/docs/SCSVS/controls/SCSVS-ORACLE-1.md)
  * [SCSVS-ORACLE-2 S7.2 算術の完全性 (Arithmetic Integrity)](Document/docs/SCSVS/controls/SCSVS-ORACLE-2.md)
* [SCSVS-BLOCK S8. サービス拒否 (Denial of Service (DoS))](Document/docs/SCSVS/12-SCSVS-BLOCK.md)
  * [SCSVS-BLOCK-1 S8.1 ガス制限 (Gas Limits)](Document/docs/SCSVS/controls/SCSVS-BLOCK-1.md)
  * [SCSVS-BLOCK-2 S8.2 資源枯渇に対する耐性 (Resilience Against Resource Exhaustion)](Document/docs/SCSVS/controls/SCSVS-BLOCK-2.md)
* [SCSVS-BRIDGE S9. ブロックチェーンデータと状態管理 (Blockchain Data and State Management)](Document/docs/SCSVS/13-SCSVS-BRIDGE.md)
  * [SCSVS-BRIDGE-1 S9.1 状態管理 (State Management)](Document/docs/SCSVS/controls/SCSVS-BRIDGE-1.md)
  * [SCSVS-BRIDGE-2 S9.2 データプライバシー (Data Privacy)](Document/docs/SCSVS/controls/SCSVS-BRIDGE-2.md)
  * [SCSVS-BRIDGE-3 S9.3 イベントログ記録 (Event Logging)](Document/docs/SCSVS/controls/SCSVS-BRIDGE-3.md)
  * [SCSVS-BRIDGE-4 S9.3.B ログ解析 (Log Analysis)](Document/docs/SCSVS/controls/SCSVS-BRIDGE-4.md)
* [SCSVS-DEFI S10. ガスの使用量、効率、制限 (Gas Usage, Efficiency, and Limitations)](Document/docs/SCSVS/14-SCSVS-DEFI.md)
  * [SCSVS-DEFI-1 S10.1 ガス使用の最適化 (Optimizing Gas Usage)](Document/docs/SCSVS/controls/SCSVS-DEFI-1.md)
  * [SCSVS-DEFI-2 S10.2 効率的なコントラクト設計 (Efficient Contract Design)](Document/docs/SCSVS/controls/SCSVS-DEFI-2.md)
* [SCSVS-COMP S11. コンポーネント固有のセキュリティ (Component-Specific Security)](Document/docs/SCSVS/15-SCSVS-COMP.md)
  * [SCSVS-COMP-1 S11.1 トークン (ERC20, ERC721, ERC1155) (Tokens (ERC20, ERC721, ERC1155))](Document/docs/SCSVS/controls/SCSVS-COMP-1.md)
  * [SCSVS-COMP-2 S11.2 NFT セキュリティ (NFT Security)](Document/docs/SCSVS/controls/SCSVS-COMP-2.md)
  * [SCSVS-COMP-3 S11.3 保管室 (Vaults)](Document/docs/SCSVS/controls/SCSVS-COMP-3.md)
  * [SCSVS-COMP-4 S11.4 流動的ステーキング (Liquid Staking)](Document/docs/SCSVS/controls/SCSVS-COMP-4.md)
  * [SCSVS-COMP-5 S11.5 流動性プール (AMM) (Liquidity Pools (AMMs))](Document/docs/SCSVS/controls/SCSVS-COMP-5.md)
  * [SCSVS-COMP-6 S11.6 Uniswap V4 フック (Uniswap V4 Hook)](Document/docs/SCSVS/controls/SCSVS-COMP-6.md)

## OWASP スマートコントラクト Top 10 2025 日本語版

* [OWASP スマートコントラクト Top 10 2025](Document/docs/sctop10/index.md)
* SC01:2025 - [不適切なアクセス制御 (Improper Access Control)](Document/docs/sctop10/SC01-AccessControlVulnerabilities.md)
* SC02:2025 - [価格オラクル操作 (Price Oracle Manipulation)](Document/docs/sctop10/SC02-PriceOracleManipulation.md)
* SC03:2025 - [ロジックエラー (Logic Errors)](Document/docs/sctop10/SC03-LogicErrors.md)
* SC04:2025 - [入力バリデーションの欠如 (Lack of Input Validation)](Document/docs/sctop10/SC04-LackOfInputValidation.md)
* SC05:2025 - [再入可能性 (Reentrancy)](Document/docs/sctop10/SC05-Reentrancy.md)
* SC06:2025 - [チェックされていない外部呼び出し (Unchecked External Calls)](Document/docs/sctop10/SC06-UncheckedExternalCalls.md)
* SC07:2025 - [フラッシュローン攻撃 (Flash Loan Attacks)](Document/docs/sctop10/SC07-FlashLoanAttacks.md)
* SC08:2025 - [整数オーバーフローとアンダーフロー (Integer Overflow and Underflow)](Document/docs/sctop10/SC08-IntegerOverUnderFlow.md)
* SC09:2025 - [安全でないランダム性 (Insecure Randomness)](Document/docs/sctop10/SC09-InsecureRandomness.md)
* SC10:2025 - [サービス拒否 (Denial Of Service)](Document/docs/sctop10/SC10-DenailOfService.md)

## License

[Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/)

## Translator (Japanese)

[Koki Takeyama](https://github.com/coky-t)

- Document Site - <https://coky-t.gitbook.io/owasp-docs-ja/>
- Document Repository - <https://github.com/coky-t/owasp-docs-ja>
