---
hide:
  - toc
search:
  exclude: true
---

# OWASP SCSVS

<!-- <img src="../assets/scsvs_cover.png" align="right" style="border-radius: 3px; margin: 3em; box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;" width="350px" > -->

<!-- <a href="https://github.com/OWASP/www-project-smart-contract-security-verification-standard/">:material-github: GitHub Repo</a> -->

<!-- <a href="https://owasp.org/www-project-smart-contract-security-verification-standard">:material-web: OWASP Project Page</a> -->

**スマートコントラクトセキュリティ検証標準 (SCSVS)** は、主に Solidity で記述され、EVM ベースのブロックチェーンにデプロイされるスマートコントラクトのに対する特定のセキュリティ要件やテストのリストです。これらの要件は、アーキテクト、開発者、テスト担当者、セキュリティ専門家、ツールベンダー、利用者が、安全なスマートコントラクト、分散型アプリケーション (dApps)、ブロックチェーンプロトコルを定義、構築、テスト、検証するために使用することを目的としています。この標準は、スマートコントラクトと分散型金融 (DeFi) システムのセキュリティと完全性を確保するためのベストプラクティスを推進するものです。

SCSVS を補完するために、OWASP SCS プロジェクトでは [OWASP スマートコントラクトセキュリティテストガイド (Smart Contract Security Testing Guide, SCSTG)](https://scs.owasp.org/SCSTG)、[OWASP スマートコントラクト脆弱性一覧 (Smart Contract Weakness Enumeration, SCWE)](https://scs.owasp.org/SCWE)、[OWASP SCS チェックリスト](https://scs.owasp.org/checklists) も提供しています。これらは OWASP SCSVS にリストされているコントロールを検証し、コンプライアンスを証明するのに最適なコンパニオンです。

<br>

<!-- <button class="scs-button" onclick="window.location.href='https://github.com/OWASP/www-project-smart-contract-security-verification-standard/releases/download/v0.0.1/OWASP_Smart_Contract_Security_Verification_Standard-0.0.1_en.pdf';"> Download the SCSVS</button> -->

<br>

## SCSVS コントロールグループ

この標準は **SCSVS-XXXXX** とラベル付けしたさまざまなコントロールグループに分かれており、スマートコントラクト攻撃対象領域の中で最も重要な領域を表しています。

- **SCSVS-ARCH**: ブロックチェーンシステムの安全なアーキテクチャ、設計原則、脅威モデリングプラクティス。
- **SCSVS-CODE**: スマートコントラクトの安全な開発、テスト、デプロイメントのためのポリシーと手順。
- **SCSVS-GOV**: 操作や悪用を防ぐためのビジネスロジックのと経済メカニズムのセキュリティ。
- **SCSVS-AUTH**: ブロックチェーンベースのアプリケーションのための堅牢なアクセス制御と認証手段。
- **SCSVS-COMM**: コントラクト、ユーザー、外部システム間の安全な通信とインタラクション。
- **SCSVS-CRYPTO**: ブロックチェーンアプリケーションでの暗号実装のベストプラクティス。
- **SCSVS-ORACLE**: 算術演算と論理演算が安全で攻撃に耐性があることの確保。
- **SCSVS-BLOCK**: サービス拒否 (DoS) 攻撃とリソース枯渇リスクの緩和。
- **SCSVS-BRIDGE**: ブロックチェーンの完全性を維持するための効率的なデータおよび状態管理プラクティス。
- **SCSVS-DEFI**: 非効率性や制約を回避するためのガス使用の最適化と監視。
- **SCSVS-COMP**: ブロックチェーンアプリケーションに合わせたコンポーネント固有のセキュリティガイドライン。


### !!! 警告 「SCS テストプロファイル」

SCS プロジェクトは従来、三つの検証レベル (L1, L2, L3) を提供してきましたが、2024 年の SCSVS リファクタリング時に再検討され、「SCS テストプロファイル」として作り直されました。
<br><br>
再配置の間、一時的な措置として、[OWASP SCS チェックリスト](https://scs.owasp.org/checklists) には現在の SCSTG v0.0.1 テストに関連付けられた検証レベルを引き続き含みます。

<br><br>
