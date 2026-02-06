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
トランザクション順序の依存性 (TOD) はコントラクトの実行結果がトランザクションの順序に依存する場合に発生します。攻撃者は特定の順序でトランザクションを送信することでこの問題を悪用し、コントラクトの状態を操作し、他のトランザクションのフロントランニングやバックランニングといった不当な優位性を獲得できます。これは予期しない動作やリソース消費につながる可能性があります。

## 対策
TOD 脆弱性を緩和するには、コントラクトのロジックがトランザクションの順序に依存しないようにします。コミット公開スキームやランダム化などの技法を使用して、攻撃者がトランザクションの順序を予測して悪用することを防ぎます。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

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
pragma solidity ^0.8.0;

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
