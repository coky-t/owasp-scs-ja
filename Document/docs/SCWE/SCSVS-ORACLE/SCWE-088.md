---
title: 価格ベースの計算における不適切な Decimal 正規化 (Improper Decimal Normalization in Price-Based Calculations)
id: SCWE-088
alias: Improper-Decimal-Normalization-in-Price-Based-Calculations
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-1]
  cwe: [682]
status: new
---

## 関連
- **CWE-681**:  [Incorrect Conversion between Numeric Types](https://cwe.mitre.org/data/definitions/681.html)
- **CWE-682**:  [Incorrect Calculation](https://cwe.mitre.org/data/definitions/682.html)


## 説明
この問題は、スマートコントラクトがトークン値と価格フィードを含む演算を、異なるソース間 (例: 小数点以下の桁数が変化する ERC-20 トークンと、小数点以下の 8 桁で固定された価格を持つ Chainlink オラクル) で小数点以下の桁を適切に揃えずに実行する場合に発生します。計算前に小数点以下の桁数を正規化しないと、結果が大幅に膨張したり収縮することにつながり、ユーザーに過剰な請求、過小な支払い、あるいは経済的搾取を引き起こす可能性があります。

## 影響
スマートコントラクトは、トークンでの手数料支払いや、価格フィードを含む担保ロジックの使用など、資産間の変換にしばしば依存しています。これらの計算のおける不適切な小数点桁数の処理は以下の可能性があります。
- 過剰請求や過少支払いにつながる。
- ヴォールトまたは会計ロジックにシステム的な金融不均衡をもたらす。
- 丸め誤差や精度のギャップを裁定取引することで経済的搾取を可能にする。
- 小数点以下が偶然一致してもテスト時に検出されない。
この問題は特に以下の場合に深刻です。
- 18 桁以外の小数点を持つ ERC-20 トークン (例: USDC = 6) が関係している。
- Chainlink オラクルは 8 桁の固定小数点価格を返す。
- 演算はネイティブ ETH 値 (18桁) とトークン値または価格が混在している。
---
## 対策
- 関連するすべてのアセットとフィード間で、常に小数点桁数を正規化します。
- すべての価格/トークン変換に一元的なユーティリティ関数を使用します。
- 外れ値を検出するためのサニティチェックを追加します (例: 出力が予想範囲の 1000 倍を超える場合は元に戻す)。
- 可能であれば、監査のために中間計算ステップを出力します。

## 事例

- **❌ 脆弱なコード (小数点桁数正規化の欠落)**
```solidity
// Assume ETH/USD = 3000e8 (8 decimals), Token/USD = 1e8, and token has 6 decimals
uint256 tokenAmount = (ethAmountInWei * ethPriceInUsd) / tokenPriceInUsd;
// Result is in 18-decimal scale, not adjusted for the 6-decimal USDC token
```

- **✅ 安全なコード (適切な小数点揃えあり)**
```solidity
uint8 tokenDecimals = IERC20(token).decimals();

uint256 rawAmount = (ethAmountInWei * ethPriceInUsd) / tokenPriceInUsd;
uint256 adjustedAmount = rawAmount / (10 ** (18 - tokenDecimals));
// Now 'adjustedAmount' is in correct units for the target token
```


## 現実的なエクスプロイトの例
仮説:

```solidity
Registrar fee = 0.01 ETH = 1e16 wei

ETH/USD price = 3000 * 1e8 = 300000000000

USDC/USD price = 1 * 1e8 = 100000000

USDC has 6 decimals

Faulty Calculation:

result = (1e16 * 300000000000) / 100000000 = 3e19
return result / 1e6 = 3e13 (30 trillion base units = 30 million USDC)
Expected:

0.01 ETH * $3,000 = $30
→ 30 * 1e6 = 30,000,000 USDC units

Overcharge:

Actual charged: 30,000,000 USDC
Expected: 30 USDC
Overcharge: 999,900x
```
---
