---
title: 非推奨の Solidity 関数の使用 (Use of Deprecated Solidity Functions)
id: SCWE-072
alias: use-of-deprecated-solidity-functions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-1]
  cwe: [676]
status: new
---

## 関連
- CWE-676: Use of a Dangerous Function
  [https://cwe.mitre.org/data/definitions/676.html](https://cwe.mitre.org/data/definitions/676.html)

## 説明
Some Solidity functions and features have been deprecated over time due to security risks, inefficiencies, or better alternatives being introduced in newer versions of Solidity. Using deprecated functions can expose contracts to known vulnerabilities and potential attacks. Examples include `suicide()`, which was replaced by `selfdestruct()`, and `sha3()`, which was replaced by `keccak256()`.

## 対策
Always check the Solidity documentation to ensure that the functions you're using are not deprecated. If a function is deprecated, replace it with its recommended alternative to maintain the contract's security and ensure compatibility with future Solidity versions. **Note:** As of EIP-6049 (Solidity 0.8.24+), `selfdestruct` is deprecated and will be removed in a future EVM version.

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function oldFunction() public {
        // Using deprecated function `suicide`
        suicide(msg.sender);  // Deprecated, should be replaced with `selfdestruct`
    }
}
```

### 修正したコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function oldFunction() public {
        // Correctly using the recommended alternative `selfdestruct` (deprecated per EIP-6049; prefer migration patterns)
        selfdestruct(payable(msg.sender));  // Replacing deprecated `suicide` with `selfdestruct`
    }
}
```
