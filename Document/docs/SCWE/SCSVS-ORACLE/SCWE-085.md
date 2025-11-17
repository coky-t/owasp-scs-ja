---
title: オラクルの最小/最大価格帯の検証なしでの誤用 (Misuse of Oracle Min/Max Price Band Without Validation)
id: SCWE-085
alias: Misuse-of-Oracle-Min_Max-Price-Band-Without-Validation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-1]
  cwe: [345]
status: new
---

## 関連

- **CWE-345**: [Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
- **CWE-20**:  [Improper Input Validation](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

---

## 説明

この弱点は、スマートコントラクトが価格オラクルデータ (例: Chainlink から) を、返された価格が想定範囲または信頼できる範囲 (例: `minPrice`/`maxPrice`) 内に収まっているかどうかを検証せずに使用する場合に発生します。オラクルは、実際の価格が内部制限を超えた場合、またはフィードがデータの不安定性に遭遇した場合に、フォールバックの下限値または上限値を返すことがあります。

このようなエッジケース値を検出して拒否できないと、スマートコントラクトに不正確または悪用可能なロジックパスをもたらす可能性があります。この弱点は攻撃者に以下を許す可能性があります。

- 不適切な価格のスワップや資産変換をトリガーする。
- 担保閾値を悪用する。
- オークション価格や段階的報酬体系を操作する。
- 価格加重に基づくガバナンスシステムに影響を与える。

これらの問題はボラティリティが高い時期や部分的なオラクル停止の時期には特に危険であり、フォールバック値が自動的に返される可能性があります。

---

## 対策

To mitigate this risk:

- Always verify that the oracle-provided price lies within an expected range.
- Check whether the returned value is a fallback boundary by comparing it against known `minPrice`/`maxPrice` values.
- Monitor and log unusual oracle values for off-chain alerts and incident response.

---

## 事例

- Vulnerable contract 

```solidity
(int256 price,,,) = AggregatorV3Interface(oracle).latestRoundData();

// Uses price directly without checking for boundary values
uint256 tokenAmount = uint256(price) * userInput;
```
- Fixed Contract 

```solidity
(int256 price,,,) = AggregatorV3Interface(oracle).latestRoundData();

// Validate that the price is within trusted bounds
require(price > minPrice && price < maxPrice, "Price out of expected bounds");

uint256 tokenAmount = uint256(price) * userInput;
```
This ensures that the contract rejects unexpected oracle values that might be returned during errors or manipulation attempts.

## 参考情報
- **Chainlink Docs**: [Chainlink Price Feeds Documentation](https://docs.chain.link/data-feeds)
- **CWE-345**: [Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
- **CWE-20**:  [Improper Input Validation](https://cwe.mitre.org/data/definitions/20.html)
