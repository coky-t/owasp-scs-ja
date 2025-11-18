---
title: オラクルレスポンスフィールドの検証の欠如 (古いデータまたは不完全なデータ) (Missing Validation of Oracle Response Fields (Stale or Incomplete Data))
id: SCWE-086
alias: missing-oracle-response-validation
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

## 説明
この弱点は、スマートコントラクトがオラクル (Chainlink など) からのデータを使用する際に、`answeredInRound`、`timestamp`、あるいは `answer` 自体などのレスポンス内の重要なフィールドを検証しない場合に発生します。これらのフィールドを検証しないと、以下につながる可能性があります。

- 古いオラクルラウンドからの古い価格データの使用。
- 不完全なオラクルレスポンス (`timestamp == 0` など) の受け入れ。
- 無効または価格ゼロのデータに基づく実行。

これは、正確で最新のデータフィードに依存する DeFi プロトコルやスマートコントラクトのセキュリティに深刻な影響を及ぼす可能性があります。

## 対策
- **Validate `answer` field:** Ensure the value returned is greater than zero and not malformed.
- **Check `answeredInRound >= roundId`:** Confirms that the data is not from a stale round.
- **Verify `timestamp != 0`:** Ensures that the oracle actually returned a complete result.

Additional best practices include:
- Using fallback mechanisms or thresholds for deviation checks.
- Halting sensitive functions if oracle data is suspect or missing.

## 事例

- **❌ Vulnerable Code (No Response Validation)**  
    ```solidity
    (, int256 answer,,,) = AggregatorV3Interface(oracle).latestRoundData();
    require(uint256(answer) > 0, "Zero price"); // Minimal check only
    ```

- **✅ Secure Code (With Full Oracle Validation)**  
    ```solidity
    (uint80 roundID, int256 answer,, uint256 timestamp, uint80 answeredInRound) = 
        AggregatorV3Interface(oracle).latestRoundData();

    require(answer > 0, "Invalid price: <= 0");
    require(answeredInRound >= roundID, "Stale round data");
    require(timestamp != 0, "Incomplete oracle response");
    ```

## 参考情報
- [Chainlink Oracle Security Best Practices](https://docs.chain.link/data-feeds/security/)
