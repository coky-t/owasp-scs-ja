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
- イベントにログ記録されたデータが実際のコントラクト状態を正確に表していることを確保します。
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
- Withdraw イベントは、要求された `withdrawal` 金額である `_amount` をログ記録しますが、転送が失敗する場合 (ガス制限、再入可能性、または外部の問題により)、イベントは依然として引き落としが発生したかのようにログ記録します。
- 攻撃者がイベントログと実際の状態変化の間の不一致を悪用すると、ユーザー、外部インデクサー、オフチェーンサービスを誤解される可能性があります。

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
### 安全なコードの修正内容
- `call{value: _amount}("")` を使用して資金を安全に送金し、残高を更新する前に送金が成功したことを確認します。
- 実際の引き落とし金額を `(beforeBalance - afterBalance)` で計算し、正確なログ記録を確保します。
- トランザクションが成功した場合にのみイベントをログ記録することで、間違ったイベント発行を防ぎます。
