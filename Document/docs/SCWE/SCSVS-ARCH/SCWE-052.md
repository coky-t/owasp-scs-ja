---
title: トランザクション順序の依存性 (Transaction Order Dependence)
id: SCWE-052
alias: transaction-order-dependence
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-3]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)  

## 説明
Transaction Order Dependence (TOD) occurs when the outcome of a contract's execution depends on the order of transactions. Attackers can exploit this issue by submitting transactions in a specific order, manipulating the contract's state and gaining an unfair advantage, such as front-running or back-running other transactions. This can lead to unexpected behavior and resource consumption.

## 対策
To mitigate TOD vulnerabilities, ensure that the contract's logic does not depend on transaction order. Use techniques like commit-reveal schemes or randomization to prevent attackers from predicting the transaction order and exploiting it.

### 脆弱なコントラクトの例
```solidity
contract TODExample {
    address public winner;
    
    function bid() public payable {
        require(msg.value > 1 ether, "Bid too low");
        winner = msg.sender;  // Dependent on transaction order
    }
}
```
### 修正したコントラクトの例
```solidity
contract FixedTODExample {
    address public winner;
    uint public highestBid;
    
    function bid() public payable {
        require(msg.value > highestBid, "Bid too low");
        highestBid = msg.value;
        winner = msg.sender;
    }
}
```
