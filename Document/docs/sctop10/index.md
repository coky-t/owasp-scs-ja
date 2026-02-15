---
hide:
  - toc
search:
  exclude: true
---

# OWASP スマートコントラクト Top 10: 2026

<!-- <a href="https://github.com/OWASP/www-project-smart-contract-top-10">:material-github: GitHub Repo</a> · <a href="https://owasp.org/www-project-smart-contract-top-10">:material-web: OWASP Project Page</a> -->

<!-- <img src="../assets/OWASP-2026-Smart-Contract.png" 
     align="right" 
     style="border-radius: 3px; margin: 0 0 1em 1em; box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;" 
     width="550px" 
     height="400px"> -->

**OWASP スマートコントラクト Top 10: 2026** は Web3 開発者とセキュリティチームに、スマートコントラクトで見つかった上位 10 の脆弱性についての洞察を提供することを目的とした、標準認識ドキュメントです。

- **認識**: スマートコントラクトに影響を及ぼす最も一般的で重大な脆弱性を理解します。
- **予防**: これらの既知の問題に対して保護するためのベストプラクティスを実装します。
- **標準コンプライアンス**: スマートコントラクトの安全な開発と評価を確保するために参照します。

これは近年悪用または発見された最も重大な弱点に対してスマートコントラクトが保護されていることを確認するためのリファレンスとして役立ちます。**スマートコントラクト Top 10** は他の OWASP SCS プロジェクトと併用することで包括的なリスクカバレッジを確保できます。

> 注: 現在の **2026** Top 10 は **将来予測** です。その順位付けとカテゴリ定義は **2025 年に収集されたセキュリティインシデントと調査データ** に基づいており、翌年に最も重大になると予想されるリスクを予測するために使用されます。言い換えると、2025 年の侵害および脆弱性データは実証的な基盤を提供し、2026 年のリストはそれらの観察結果が近い将来にどのように予想されるかを反映しています。
>  
> このランキングは、最も一般的に発生して影響のある 10 のスマートコントラクトリスクについて、セキュリティ研究者、監査担当者、開発者、プロトコル所有者、および業界全体での意識を高めることを目的としています。

> [!NOTE]
> "Open Call: 2026 Top 10 Survey"  
> **目的:** Web3 セキュリティコミュニティから構造化され匿名化された実世界のシグナルを収集し、現在の 2025 年のリストの順序変更、当たらな脆弱性クラス、ギャップに関する情報を提供します。
>
> **対象貢献者:** スマートコントラクト監査担当者、プロトコルセキュリティリーダー、L1/L2 インフラチーム、ウォレット/カストディエンジニア、インシデント対応、バグバウンティ、レッド/ブルーチーム実施担当者。
>
> **アクション:** 2025 年の経験がある場合は [サーベイに参加してください](https://forms.gle/1vCRSrjYvhUgBonr8)。

<!-- <button class="scs-button" onclick="window.location.href='https://owasp.org/www-project-smart-contract-top-10/';"> Visit the Smart Contract Top 10</button> -->


### ランキングの裏側

The methodology, statistics, and data sources behind the 2026 ordering are documented in:

- [Ranking Methodology](https://github.com/OWASP/owasp-scs/blob/main/docs/sctop10/methodology.md) — Elicitation model, aggregation formulas, computed statistics, and reasoning.
- [Data Sources](https://github.com/OWASP/owasp-scs/blob/main/docs/sctop10/data-sources.md) — Practitioner survey, 2025 incident data, and external references.

## 変更内容

![OWASP 2025 to 2026 Mapping](../assets/Top10mapping2025-2026.png)

### 2026 ランキング

* SC01:2026 - [アクセス制御の脆弱性 (Access Control Vulnerabilities)](SC01-AccessControlVulnerabilities.md)
* SC02:2026 - [ビジネスロジックの脆弱性 (Business Logic Vulnerabilities)](SC02-BusinessLogicVulnerabilities.md)
* SC03:2026 - [価格オラクル操作 (Price Oracle Manipulation)](SC03-PriceOracleManipulation.md)
* SC04:2026 - [フラッシュローンを利用した攻撃 (Flash Loan–Facilitated Attacks)](SC04-FlashLoanAttacks.md)
* SC05:2026 - [入力バリデーションの欠如 (Lack of Input Validation)](SC05-LackOfInputValidation.md)
* SC06:2026 - [チェックされていない外部呼び出し (Unchecked External Calls)](SC06-UncheckedExternalCalls.md)
* SC07:2026 - [算術エラー (Arithmetic Errors)](SC07-ArithmeticErrors.md)
* SC08:2026 - [再入攻撃 (Reentrancy Attacks)](SC08-ReentrancyAttacks.md)
* SC09:2026 - [整数オーバーフローとアンダーフロー (Integer Overflow and Underflow)](SC09-IntegerOverflowUnderflow.md)
* SC10:2026 - [プロキシとアップグレード可能性の脆弱性 (Proxy & Upgradeability Vulnerabilities)](SC10-ProxyAndUpgradeabilityVulnerabilities.md)

### 概要

| タイトル | 説明 |
| -- | -- |
| SC01 - Access Control Vulnerabilities | Access control flaws allow unauthorized users or roles to invoke privileged functions or modify critical state, often leading to full protocol compromise when admin, governance, or upgrade paths are exposed. |
| SC02 - Business Logic Vulnerabilities | Design-level flaws in lending, AMM, reward, or governance logic that break intended economic or functional rules, enabling attackers to extract value even when low-level checks appear correct. |
| SC03 - Price Oracle Manipulation | Weak oracles and unsafe price integrations that let attackers skew reference prices, enabling under-collateralized borrowing, unfair liquidations, and mispriced swaps as part of larger exploit chains. |
| SC04 - Flash Loan–Facilitated Attacks | Attacks that use large, uncollateralized flash loans to magnify small bugs (in logic, pricing, or arithmetic) into large drains, by executing complex multi-step sequences in a single transaction. |
| SC05 - Lack of Input Validation | Missing or weak validation of user, admin, or cross-chain inputs that allows unsafe parameters to reach core logic, corrupting state, breaking assumptions, or enabling direct fund loss. |
| SC06 - Unchecked External Calls | Unsafe interactions with external contracts or addresses where failures, reverts, or callbacks are not safely handled, often enabling reentrancy or inconsistent state. |
| SC07 - Arithmetic Errors | Subtle bugs in integer math, scaling, and rounding; especially in share, interest, and AMM calculations; that can be repeatedly exploited to cause precision loss, or siphon value, particularly when paired with flash loans. |
| SC08 - Reentrancy Attacks | Situations where external calls can re-enter vulnerable functions before state is fully updated, allowing repeated withdrawals or state changes from outdated views of contract state. |
| SC09 - Integer Overflow and Underflow | Dangerous arithmetic on platforms or code paths without robust overflow checks, leading to wrapped values, broken invariants, and potential drains of liquidity or mis-accounting. |
| SC10 - Proxy & Upgradeability Vulnerabilities | Misconfigured or weakly governed proxy, initialization, and upgrade mechanisms that let attackers seize control of implementations or reinitialize critical state. |

### 選外次点

These categories did not rank in the 2026 Top 10 but remain relevant and should be considered during design and audit:

- **Permit front-running & nonce DoS** — An attacker can front-run a `permit()` call to consume the user's nonce, causing the original deposit/stake transaction to revert. Affects protocols that compose permit within larger flows; no fallback approval path can cause permanent DoS. 
- **Front-Running & MEV** — Transaction ordering and sandwich attacks can extract value from users. Consider MEV protection (e.g., private mempools, commit–reveal) for sensitive flows.
- **Cross-chain MEV (Symbiosis, Aug–Oct 2025)** — Source-chain event leakage enabled sandwich attacks on the destination chain before transactions reached the mempool; attackers extracted $5.27M. Demonstrates cross-chain information asymmetry and MEV in bridged flows.
- **DoS & Griefing via Gas, Loops, and State Bloat** — Poorly bounded loops, attacker-controlled iteration, and unbounded state growth (e.g., ever-growing arrays/mappings) can make critical functions unaffordable or permanently unusable, enabling economic denial of service without direct fund theft.
- **Governance-Specific Attack Vectors** — Malicious or rushed proposals, flash-loan-amplified voting power, timelock bypasses, and weak quorum/threshold design can be abused to seize protocol control, drain treasuries, or disable safety mechanisms under the guise of governance.
- **Cryptographic & Signature-Scheme Vulnerabilities** — Ambiguous or non-standard signing flows (e.g., misuse of signature-related EIPs), replayable or cross-domain signatures, and incorrect domain separation can allow attackers to reuse approvals or authorizations beyond their intended scope.

### スマートコントラクトを超えて: 別の Top 15 Web3 攻撃ベクトル

Many of the largest losses in Web3 for the year 2025 stem from **off-chain and operational** threats rather than solely smart contract bugs: multisig manipulation/hijacking, supply chain attacks, drainer malware, fake interviews, phishing, and exchange breaches. The **Alternate Top 15** catalogues these non-smart-contract attack vectors.

![OWASP Web3 Attack Vectors Top 15 2026](../assets/alternateWA-top15.png)

<br>

<!-- <button class="scs-button" onclick="window.location.href='Web3-Attack-Vectors-Top15/';"> View Alternate Top 15 Attack Vectors</button> -->

### データソース

The 2026 Top 10 is anchored in **2025 smart contract incident data** and **practitioner input**, as detailed on the dedicated Data Sources page. In summary:

**Key 2025 incident insights (Smart Contract-only):**

- **Total protocols analysed:** 122 smart-contract incidents (deduplicated).
- **Total loss analysed:** ≈ **$905.4M** in 2025 smart-contract involved losses only.
- **Role in the ranking:** Validates and supports the 2026 Top 10 ordering.

### SolidityScan の Web3HackHub

![SolidityScan Web3HackHub 2025](../assets/solidityscan-web3hackhub2025.png)

<!-- <button class="scs-button" onclick="window.location.href='data-sources/';"> View Data Sources</button> -->

## ライセンス

The OWASP Smart Contract Top 10 (2026) is licensed under the [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/), the Creative Commons Attribution-ShareAlike 4.0 license. Some rights reserved.
