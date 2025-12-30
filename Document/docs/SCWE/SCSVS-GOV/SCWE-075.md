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
- **Use `address(this).balance`** instead of manually tracking Ether balance.
- **Prevent external Ether deposits** by disabling the fallback function unless explicitly needed.
- **Ensure proper accounting** by always reconciling balances before allowing withdrawals.

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
**Why is this vulnerable?**
- The contract does not account for direct Ether transfers outside deposit().
- An attacker can send Ether via `selfdestruct()`, inflating the contract balance without updating balance.
- This can lead to withdrawals being blocked or excessive withdrawals.

### 修正したコントラクトの例

```solidity
// ✅ Secure implementation that tracks actual balance correctly
contract CorrectBalanceTracking {
    function deposit() public payable {}

    function withdraw(uint _amount) public {
        require(address(this).balance >= _amount, "Insufficient funds");  // ✅ Correct balance check
        payable(msg.sender).transfer(_amount);
    }

    // Optional: Prevent direct Ether transfers
    receive() external payable {
        revert("Direct deposits not allowed");
    }
}
```
**Why is this secure?**
- Uses `address(this).balnce` for accurate balance tracking.
- Prevents external deposits unless explicitly intended.
- No manual `balance` variable, reducing risk of inconsistencies.

---
