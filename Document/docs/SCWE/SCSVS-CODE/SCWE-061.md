---
title: 古いコンパイラバージョン (Outdated Compiler Version)
id: SCWE-061
alias: outdated-compiler-version
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-2]
  cwe: [1103]
status: new
---

## 関連
- CWE-1103: Use of Outdated or Insecure Software
  [https://cwe.mitre.org/data/definitions/1103.html](https://cwe.mitre.org/data/definitions/1103.html)

## 説明
Using an outdated compiler version can expose a smart contract to vulnerabilities that have already been patched in newer versions. Compiler versions often include security fixes, optimizations, and new features that are crucial for the safety and performance of contracts.

## 対策
To mitigate this vulnerability, always use the most up-to-date stable version of the Solidity compiler. Ensure that your development environment is regularly updated to incorporate the latest security patches, optimizations, and features provided by newer versions of the compiler.

### 脆弱なコントラクトの例
```solidity
pragma solidity 0.4.24;  // Outdated compiler version

contract Vulnerable {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}
```

### 修正したコントラクトの例
```solidity
pragma solidity 0.8.4;  // Updated compiler version for better security and performance

contract Fixed {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}
```
