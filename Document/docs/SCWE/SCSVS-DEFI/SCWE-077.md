---
title: レート制限の欠如 (Lack of Rate Limiting)
id: SCWE-077
alias: lack-of-rate-limiting
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-2]
  cwe: [770]
status: new
---

## 関連
- **CWE-770: Allocation of Resources Without Limits or Throttling**  
  [https://cwe.mitre.org/data/definitions/770.html](https://cwe.mitre.org/data/definitions/770.html)

## 説明
スマートコントラクトのレート制限の欠如は **サービス拒否 (DoS)** 攻撃、過剰なガス消費、コントラクト状態の肥大化につながる可能性があります。関数呼び出しの頻度を制限するメカニズムなしでは、攻撃者はコントラクトをトランザクションであふれさせ、遅延、ガス料金の増加、重要な機能の完全な利用不可を引き起こす可能性があります。

この脆弱性は特に **DeFi プロトコル** において危険であり、制限のない関数呼び出しは、資金を流出したり、ガバナンスメカニズムを悪用したり、オンチェーン処理を過負荷にする可能性があります。

## 対策
- **時間ベースの制約** (呼び出しの間にクールダウン期間を要求するなど) などの **レート制限メカニズム** を実装します。
- **有効期限タイムスタンプ付きカウンタ** を使用して、反復的なアクションを追跡および制限します。
- **ガス料金またはステーキング要件** を導入して、スパムトランザクションを阻止します。

### 脆弱なコントラクトの例
```solidity
contract Example {
    mapping(address => uint) public userRequests;

    // ❌ No rate limiting: users can spam this function indefinitely
    function request() public {
        userRequests[msg.sender]++;
        // No restrictions or cooldowns, allowing abuse
    }
}
```

**Why is this vulnerable?**
- No restrictions on how frequently a user can call `request()`.
- An attacker can spam transactions, leading to high gas costs and DoS.
- Storage bloat from excessive `userRequests` mappings.

### 修正したコントラクトの例

```solidity
contract SecureExample {
    struct RequestData {
        uint count;
        uint lastReset;
    }

    mapping(address => RequestData) public userRequests;
    uint public constant REQUEST_LIMIT = 5;
    uint public constant TIME_WINDOW = 1 hours;

    // ✅ Implement rate limiting with a cooldown period
    function request() public {
        RequestData storage requestData = userRequests[msg.sender];

        if (block.timestamp > requestData.lastReset + TIME_WINDOW) {
            requestData.count = 0;  // Reset count after time window
            requestData.lastReset = block.timestamp;
        }

        require(requestData.count < REQUEST_LIMIT, "Rate limit exceeded");
        requestData.count++;
    }
}
```
**Why is this safe?**
- Tracks request timestamps, resetting counts after a fixed period.
- Restricts excessive calls, preventing abuse and DoS attacks.
- Efficient state management, reducing unnecessary storage costs.

**By enforcing rate limits, contracts can prevent spam attacks and ensure fair access to on-chain resources.**
