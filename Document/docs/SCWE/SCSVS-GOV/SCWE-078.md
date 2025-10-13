---
title: Ether 送金の不適切な取り扱い (Improper Handling of Ether Transfers)
id: SCWE-078
alias: improper-ether-transfer-handling
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [703]
status: new
---

## 関連
- CWE-703: Improper Check or Handling of Exceptional Conditions
  [https://cwe.mitre.org/data/definitions/703.html](https://cwe.mitre.org/data/definitions/703.html)

## 説明
Improper handling of Ether transfers in Solidity can lead to unexpected behaviors, such as failed transactions or loss of funds. For instance, when using `transfer()` or `send()`, failure to check for successful execution or not handling exceptions correctly can cause Ether to be locked or lost in the contract.

## 対策
Always handle exceptions properly when transferring Ether. Ensure that you check for success or failure using `require()` or `assert()` after Ether transfers. Additionally, use `call()` with a specified gas limit for greater flexibility and error handling.

### 脆弱なコントラクトの例
```solidity
contract Example {
    function transferEther(address payable _to) public payable {
        // Fails silently if transfer fails
        _to.transfer(msg.value);  // No error handling, can cause issues
    }
}
```
### 修正したコントラクトの例
```solidity
contract Example {
    function transferEther(address payable _to) public payable {
        // Properly check for success
        require(_to.send(msg.value), "Transfer failed");  // Using require to check for failure
    }
}
```
