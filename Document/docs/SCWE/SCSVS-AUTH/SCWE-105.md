---
title: ドメインセパレータまたは Nonce の欠如による署名リプレイの許可 (Permit Signature Replay via Missing Domain Separator or Nonce)
id: SCWE-105
alias: permit-signature-replay
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [347]
status: new
---

## 関連
- CWE-347: Improper Verification of Cryptographic Signature  
  [https://cwe.mitre.org/data/definitions/347.html](https://cwe.mitre.org/data/definitions/347.html)

## 説明
チェーン固有のドメインセパレータを省略したり、nonce をインクリメントしなかったり、有効期限を無視するなど、不適切な EIP-2612 許可実装は、署名がチェーン間または複数回にわたってリプレイされる可能性があります。攻撃者は、フォークされたネットワーク上で、支払いを繰り返し承認したり、許可を再使用できます。

## 対策
- `name`, `version`, `chainId`, `verifyingContract` を含む EIP-712 ドメインパラメータを構築します。
- 所有者ごとの nonce を保持し、許可が成功するごとにインクリメントします。
- 期限を適用し、期限切れの署名を拒否します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;
contract Token {
    mapping(address => uint256) public nonces;

    function permit(address owner, address spender, uint256 value, bytes calldata sig) external {
        // missing chainId/domain checks, nonce not incremented
        address signer = ecrecover(/* simplified */);
        require(signer == owner, "bad sig");
        // allowance set here...
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
contract Token {
    bytes32 public DOMAIN_SEPARATOR;
    mapping(address => uint256) public nonces;

    constructor() {
        DOMAIN_SEPARATOR = keccak256(abi.encode(
            keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
            keccak256(bytes("Token")),
            keccak256(bytes("1")),
            block.chainid,
            address(this)
        ));
    }

    function permit(/* params */) external {
        // verify typed data with nonce and deadline, then increment nonce
        nonces[owner] += 1;
    }
}
```
