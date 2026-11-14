---
title: ERC721/ERC1155 Safe Transfer コールバックによる再入可能性 (Reentrancy via ERC721/ERC1155 Safe Transfer Callbacks)
id: SCWE-138
alias: erc721-erc1155-callback-reentrancy
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [841]
status: new
---

## 関連
- CWE-841: Improper Enforcement of Behavioral Workflow  
  [https://cwe.mitre.org/data/definitions/841.html](https://cwe.mitre.org/data/definitions/841.html)

## 説明
ERC721 `safeTransferFrom` および ERC1155 `safeTransferFrom` は、受信側コントラクトで `onERC721Received` または `onERC1155Received` を呼び出します。受信側が NFT を受け入れるコントラクトであり、これらのコールバック内で重要なロジック (引き落とし、状態更新など) をリエントランシー保護なしに実行している場合、攻撃者は転送時に送信側コントラクトに再入し、古い状態を悪用することが可能です。これは ERC777 フック (SCWE-104) と類似していますが、NFT に対するものです。

## 対策
- NFT 転送を実行する関数や、レシーバコールバックを介して再入される可能性のあるロジックに、`nonReentrant` ガードを追加する。
- `safeTransferFrom` を呼び出す前に、状態 (残高、会計処理など) を更新します。
- コールバックを避けるため、受信側が信頼できる場合、`safeTransferFrom` の代わりに `transferFrom` を使用することを検討します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

interface IERC721 {
    function safeTransferFrom(address from, address to, uint256 tokenId) external;
}

contract NFTVault {
    mapping(address => uint256) public deposits;
    IERC721 public nft;

    function deposit(uint256 tokenId) external {
        nft.safeTransferFrom(msg.sender, address(this), tokenId);
        deposits[msg.sender]++;
    }

    function withdraw(uint256 tokenId) external {
        require(deposits[msg.sender] > 0, "No deposit");
        nft.safeTransferFrom(address(this), msg.sender, tokenId);  // Callback here—attacker reenters
        deposits[msg.sender]--;  // Too late: attacker already reentered and passed the check
    }
}
```
**Why vulnerable:** During `withdraw`, `safeTransferFrom` calls `onERC721Received` on the recipient before the state update. A malicious contract can reenter `withdraw` in that callback, pass the `deposits[msg.sender] > 0` check again, and withdraw a second NFT (double withdrawal).

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract NFTVault is ReentrancyGuard {
    mapping(address => uint256) public deposits;
    IERC721 public nft;

    function withdraw(uint256 tokenId) external nonReentrant {
        require(deposits[msg.sender] > 0, "No deposit");
        deposits[msg.sender]--;
        nft.safeTransferFrom(address(this), msg.sender, tokenId);
    }
}
```
**Fix:** `nonReentrant` blocks reentry through the ERC721 receiver callback.
