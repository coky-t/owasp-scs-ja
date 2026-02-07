---
title: 不正な Ether 残高追跡 (Incorrect Ether Balance Tracking)
id: SCWE-075
alias: incorrect-ether-balance
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [852]  # CWE-852: Untrusted Control Sphere (External Ether Transfers)
status: new
---

## 関連
- CWE-852: Untrusted Control Sphere  
  [https://cwe.mitre.org/data/definitions/852.html](https://cwe.mitre.org/data/definitions/852.html)  

## 説明
不正な Ether 残高追跡は、コントラクトが `address(this).balance` に依存せず、手動で内部残高変数を維持している場合に発生します。これは想定した関数以外 (`selfdestruct()`, `transfer()`, 直接入金を経由するなど) で Ether を受け取った場合に不整合を引き起こします。

攻撃者は、コントラクトの見た目の残高を人為的に水増しすることでこれを悪用し、不正な引き落としやトランザクションの失敗につながる可能性があります。この問題は、実際のコントラクト残高を適切に検証しない、不備のある設計の入出金システムによくあります。

## 攻撃シナリオ
攻撃者は `selfdestruct()` を使用してコントラクトに Ether を送信し、内部追跡変数を更新せずに実際の残高を増やします。その後、ユーザーは資金を引き落とそうとしますが、コントラクトは実際よりも多くの Ether を保有すると誤って想定し、予期せぬ障害や悪用を引き起こします。

## 対策
- 手動で Ether 残高を追跡するのではなく **`address(this).balance` を使用します**。
- 明示的に必要な場合を除き、フォールバック関数を無効にすることで、**外部 Ether 入金を防止します**。
- 引き落としを許可する前に常に照合することで **適切なアカウンティングを確保します**。

### 脆弱なコントラクトの例
```solidity
// ❌ Vulnerable to incorrect balance tracking due to external Ether deposits
contract IncorrectBalanceTracking {
    uint public balance;  // ❌ Manually tracking Ether balance

    function deposit() public payable {
        balance += msg.value;
    }

    function withdraw(uint _amount) public {
        require(balance >= _amount, "Insufficient funds");
        payable(msg.sender).transfer(_amount);
        balance -= _amount;
    }
}
```
**なぜこれが脆弱なのか？**
- このコントラクトは deposit() 以外の直接 Ether 送金を考慮していません。
- 攻撃者は `selfdestruct()` を介して Ether を送信し、残高を更新することなくコントラクト残高を水増しします。
- これは引き落としをブロックされたり、過剰な引き落としにつながる可能性があります。

### 修正したコントラクトの例

```solidity
pragma solidity ^0.8.0;

// ✅ Secure implementation: per-user accounting + address(this).balance for consistency
contract CorrectBalanceTracking {
    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount, "Insufficient funds");
        require(address(this).balance >= _amount, "Contract balance insufficient");
        balances[msg.sender] -= _amount;
        (bool success, ) = payable(msg.sender).call{value: _amount}("");
        require(success, "Transfer failed");
    }

    receive() external payable {
        revert("Direct deposits not allowed");
    }
}
```
**なぜこれが安全なのか？**
- ユーザーごとに `balances` をマッピングすることで、入金者のみがその資金を引き落とすことができるようになります。
- 送金前に `address(this).balance` と照合し、予期しない流入 (例: `selfdestruct`) を検出します。
- フレキシブルガスには `call{value}` を使用します。直接入金では `receive()` が元に戻ります。

---
