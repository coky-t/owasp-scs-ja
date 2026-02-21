---
title: Bridge Mint/Burn でのトークン Decimal の不一致 (Mismatched Token Decimals in Bridge Mint/Burn)
id: SCWE-132
alias: bridge-decimal-mismatch
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-1]
  cwe: [682]
status: new
---

## 関連
- CWE-682: Incorrect Calculation  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
正規化されていない異なる Decimal でのトークンをブリッジするとラップされた資産のミント過剰や不足を招きます。攻撃者は Decimal の混乱を悪用して価値を吸い上げたり、元のチェーンに引き戻す際に資金をロックする可能性があります。

## 対策
- ミント/バーン前に金額を正規化して標準精度にします。
- トークンごとの Decimal 設定をブリッジ操作時に一貫性を検証します。
- Decimal の異なるチェーン間での往復変換のテストを追加します。

## 事例

### 脆弱
```solidity
// assumes 18 decimals on both sides; origin token has 6
_mint(user, amount);
```

### 修正済み
```solidity
uint8 srcDec = 6;
uint8 dstDec = 18;
uint256 normalized = amount * 10**(dstDec - srcDec);
_mint(user, normalized);
```
