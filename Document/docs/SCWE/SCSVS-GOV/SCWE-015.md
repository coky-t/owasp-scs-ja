---
title: 不十分なガバナンス文書 (Poor Governance Documentation)
id: SCWE-015
alias: poor-governance-documentation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [933]
status: new
---

## 関連
- CWE-933: Security Misconfiguration  
  [https://cwe.mitre.org/data/definitions/933.html](https://cwe.mitre.org/data/definitions/933.html)

- CWE-1118: Insufficient Documentation of Error Handling Techniques  
    [https://cwe.mitre.org/data/definitions/1118.html](https://cwe.mitre.org/data/definitions/1118.html)

## 説明
不十分なガバナンス文書は、スマートコントラクトシステムの意思決定プロセス、アップグレードメカニズム、ロールベースのパーミッションに関する **明確で、透明性があり、公にアクセスできる記録の欠如** を指します。適切な文書なしでは、**ユーザー、開発者、監査担当者はガバナンスモデルを理解するのに苦労し**、**信頼の低下、構成ミス、潜在的なセキュリティ脆弱性** につながります。

不十分なガバナンス文書に関連する主なリスク:
- **不明確な意思決定プロセス**: ユーザーはガバナンス提案がどのように開始、承認、実行されるかを理解できない可能性があります。
- **隠れた集中化リスク**: 重要なガバナンス権限は一般の認識なしで少数のグループに集中している可能性があります。
- **アップグレード可能性の透明性の欠如**: アップグレード手順が十分に文書化されていない場合、悪意のある変更や意図しない変更は見過ごされる可能性があります。
- **不十分なインシデント対応**: 明確な緊急プロトコルなしでは、ガバナンス不良が攻撃への対応の遅れや不十分な対応につながる可能性があります。

## 対策
- **包括的なガバナンス文書**: ロール、パーミッション、投票メカニズム、アップグレード可能性を技術的および非技術的な形式で明確に説明します。
- **公開ガバナンスレポート**: ガバナンスの決定、主要な投票、プロトコルの更新を透明性のある方法で定期的に公開します。
- **明確に定義された緊急手順**: ガバナンスの不良、プロトコル攻撃、キーホルダーの侵害に対する対応計画を策定し、文書化します。
- **オンチェーンガバナンスの可視化**: ガバナンススマートコントラクトが透明性と説明責任のために関連する機能を公開することを確保します。
- **ガバナンスメカニズムの第三者監査**: 独立した監査を通じて、ガバナンス文書とプロセスを定期的にレビューします。

## 事例
Unlike code vulnerabilities, **poor governance documentation is a process failure rather than a direct coding flaw**. However, its consequences can be illustrated with real-world governance failures:

### 事例: セキュリティ侵害につながる不明確なアップグレードポリシー
A DeFi protocol allows smart contract upgrades but **fails to document the governance process for approving upgrades**. A small group of developers, holding majority control over the governance multisig, **pushes an unreviewed upgrade** that introduces a critical vulnerability, resulting in a protocol exploit.

### 修正: 透明性のあるアップグレードポリシーの確保
Protocols should establish a clear and documented upgradeability framework outlining who can propose, review, and approve upgrades. Implementing timelocks, community review periods, and independent security audits ensures upgrades are thoroughly vetted before execution. Additionally, maintaining an on-chain governance audit log enhances accountability and prevents unauthorized changes.


### 事例: ガバナンス採択における透明性の欠如
A DAO implements an **opaque governance process**, where voting power is controlled by a few insiders without **a clear framework on how votes are counted or executed**. Community members later discover that **critical protocol changes were pushed without public discussion**, leading to governance disputes and loss of user trust.

### ガバナンス採択における透明性の向上
Governance processes should be transparent, well-documented, and resistant to centralization risks to maintain trust. Clearly defining voting mechanisms, proposal review procedures, and decision-making criteria prevents manipulation and governance capture. Public governance discussions, decentralized voting structures, and immutable records of voting outcomes further enhance credibility and community trust.

---
