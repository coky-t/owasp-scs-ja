# SCSVS-COMP-6

## S11.6 Uniswap V4 フック (Uniswap V4 Hook)

### 管理目標
Uniswap コンポーネントの安全な統合とカスタマイズを確保します。

### S11.6.A 安全な統合とカスタマイズ (Secure Integration and Customization)

| **SCSVS&nbsp;VR&nbsp;ID** | 要件                                                                 | L1 | L2 | L3 | SWE |
| ------------------------- | -------------------------------------------------------------------- | -- | -- | -- | --- |
| S11.6.A1     | Uniswap の TickMath および FullMath ライブラリの正しい使用状況を検証して、バージョン固有の Solidity の考慮事項に従って、チェックされていない算術演算の適切な処理を確保していることを検証します。 |    | ✓  | ✓  |     |
