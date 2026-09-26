---
title: 実装のアップグレード時のストレージスロットの衝突 (Storage Slot Collision When Upgrading Implementation)
id: SCWE-150
alias: storage-layout-cross-contract-upgrade
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [682]
status: new
---

## 関連
- CWE-682: Incorrect Calculation  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
プロキシの実装をアップグレードする際、新しい実装のストレージレイアウトはプロキシのストレージと互換性を有する必要があります。プロキシ自身のストレージ (admin や実装アドレスなど) を考慮せずに実装に変数を追加すると、スロットの衝突を引き起こす恐れがあります。同様に、状態変数を追加する継承コントラクトは、基底やプロキシによって使用されるスロットを上書きする恐れがあります。SCWE-099 は同じコントラクトのレイアウトをカバーしますが、これはコントラクト間 (プロキシ＋実装、継承チェーン) のレイアウトを扱います。

## 対策
- 単一のストレージコントラクトを使用するか、一貫したストレージレイアウトの規約 (例: EIP-1967) に従います。
- 将来の拡張のためにベースコントラクトにギャップ (`uint256[50] private __gap`) を備えます。
- アップグレード前にストレージレイアウト差分ツールを実行します。

## 事例

### 脆弱
```solidity
// Proxy
contract Proxy {
    address public implementation;  // slot 0
    address public admin;           // slot 1
}

// Implementation V2 - assumes it "owns" slots from 0
contract ImplV2 {
    address public owner;      // slot 0 - COLLIDES with proxy's implementation
    uint256 public newValue;   // slot 1 - COLLIDES with proxy's admin
}
```

### 修正済み
```solidity
contract ImplV2 {
    // Storage in implementation must not overlap proxy slots
    // Use EIP-1967 or append after reserved slots
    bytes32 private constant IMPLEMENTATION_SLOT = 0x360894...;
    // Implementation state starts after proxy slots
    uint256 public newValue;
}
```
