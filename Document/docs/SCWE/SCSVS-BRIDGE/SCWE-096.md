---
title: クロスチェーン NFT 引き落とし時のトークンバーンの欠如 (Missing Token Burn on During Cross-Chain NFT Withdrawal)
id: SCWE-096
alias: Missing-Token-Burn-on-During-Cross-Chain-NFT-Withdrawal
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-1]
  cwe: [345]
status: new
---

## 関連
- CWE-345: Insufficient Verification of Data Authenticity  
  [CWE-345 Link](https://cwe.mitre.org/data/definitions/345.html)
- CWE-664: Improper Control of a Resource Through Its Lifetime  
  [CWE-664 Link](https://cwe.mitre.org/data/definitions/664.html)

## 説明
この弱点は、クロスチェーントランザクションを開始する前にソースチェーン (チェーン B) 上の対応するトークンを適切にバーンまたはロックすることなく、クロスチェーンブリッジがチェーン B からチェーン A への出金を許可する場合に発生します。

結果として、同じトークンが両方のチェーンに同時に存在する可能性があり、悪意のある人物が同じトークンを複数のチェーンで売却、譲渡、または使用する二重支払いシナリオを可能にします。

## 対策

1. NFT をバーンする
    - クロスチェーン出金リクエストを送信する前に、L2 NFT コントラクトの `burn(tokenId)` 関数を呼び出します。
    - これは NFT が L2 上に存在しなくなり、再使用、譲渡、販売できなくなります。

2. あるいは、NFT をロックする (バーンができない場合)
    - NFT を永久に破棄する予定がない場合、クロスチェーン出金が正常に完了するまで L2 上のトークンを凍結するロックメカニズムを実装します。

3. クロスチェーンワークフローを更新する
    - 出金プロセスの一部としてバーン/ロック操作を強制します。
    - バーン/ロックが失敗した場合はトランザクション全体を元に戻します。


## 事例
- **脆弱なコード (クロスチェーンメッセージを送信する前にトークンをバーンしない)**
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SourceChainNFTGateway {
    mapping(address => mapping(uint256 => address)) public ownerOf;

    function withdrawNFT(address l2Token, uint256 tokenId) external {
        require(ownerOf[l2Token][tokenId] == msg.sender, "Not the owner");

        // ❌ Send cross-chain message to Destination Chain (omitted for simplicity)
        // NFT is still available on Source Chain → double-spend possible
    }
}
```

- **安全なコード (クロスチェーンメッセージを送信する前にトークンをバーンする)**
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IL2NFT {
    function burn(uint256 tokenId) external;
}

contract SourceChainNFTGateway {
    mapping(address => mapping(uint256 => address)) public ownerOf;

    function withdrawNFT(address l2Token, uint256 tokenId) external {
        require(ownerOf[l2Token][tokenId] == msg.sender, "Not the owner");

        // ✅ Burn the NFT on Source Chain to prevent double-spend
        IL2NFT(l2Token).burn(tokenId);

        // Send cross-chain message to Destination Chain (omitted for simplicity)
    }
}
```
