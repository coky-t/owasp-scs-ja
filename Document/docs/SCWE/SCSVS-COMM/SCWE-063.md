---
title: 安全でないイベント発行 (Insecure Event Emission)
id: SCWE-063
alias: insecure-event-emission
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-2]
  cwe: [778]
status: new
---

## 関連
- CWE-778: Insufficient Logging  
  [https://cwe.mitre.org/data/definitions/778.html](https://cwe.mitre.org/data/definitions/778.html)

## 説明
Solidity のイベントは、コントラクトのアクティビティのログ記録と透明性の確保において重要な役割を果たします。不適切なイベント処理は以下のようなセキュリティリスクにつながる可能性があります。

1. **重要なイベント発行の欠如** – 外部監視が困難になります。
2. **誤解を招くデータや不正確なデータの発行** – ユーザーまたは外部システムが不正確な想定を行うことにつながります。
3. **機密情報のログ記録** – プライベートデータやセキュリティ上重要なデータが漏洩します。

イベントを適切に処理できないと、コントラクトの監査、デバッグ、外部監査ツールに影響を及ぼし、異常検出やコントラクト状態の正確な追跡が困難になります。

## 対策
- トークンの転送、所有者の変更、コントラクトのアップグレードなど、すべての重要な状態変化に対してイベントを発行します。
- イベントにログ記録されたデータが実際のコントラクト状態を正確に表していることを確認します。
- 秘密鍵、認証に使用されるハッシュ、機密性の高いビジネスロジックなどの機密情報をログ記録することは避けます。

### 脆弱なコントラクトの例
```solidity
contract Example {
    mapping(address => uint) public balances;
    event Withdraw(address indexed user, uint amount);

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount, "Insufficient funds");
        payable(msg.sender).transfer(_amount);
        balances[msg.sender] -= _amount;
        
        // Incorrect event emission: Logs the requested amount instead of the actual withdrawn amount
        emit Withdraw(msg.sender, _amount);
    }
}
```

### 脆弱なコードの問題点
- The Withdraw event logs `_amount`, which is the requested `withdrawal` amount, but if the transfer fails (due to gas limits, reentrancy, or an external issue), the event still logs it as if the withdrawal happened.
- If an attacker exploits a discrepancy between event logs and actual state changes, they could mislead users, external indexers, or off-chain services.

### 修正したコントラクトの例

```solidity
contract Example {
    mapping(address => uint) public balances;
    event Withdraw(address indexed user, uint actualAmount);

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount, "Insufficient funds");

        uint beforeBalance = address(this).balance;
        (bool success, ) = payable(msg.sender).call{value: _amount}("");
        uint afterBalance = address(this).balance;

        require(success, "Transfer failed");

        uint actualWithdrawn = beforeBalance - afterBalance;
        balances[msg.sender] -= actualWithdrawn;

        // Logs the correct amount actually withdrawn
        emit Withdraw(msg.sender, actualWithdrawn);
    }
}
```
### 安全なコードの修正点
- Uses `call{value: _amount}("")` to send funds safely and ensures success before updating the balance.
- Calculates the actual withdrawn amount `(beforeBalance - afterBalance)` to ensure accurate logging.
- Prevents false event emissions by only logging an event if the transaction succeeds.
