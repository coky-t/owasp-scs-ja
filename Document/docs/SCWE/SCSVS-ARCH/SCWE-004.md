---
title: 捕捉されない例外 (Uncaught Exceptions)
id: SCWE-004
alias: uncaught-exceptions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [703]
status: new
---

## 関連
- CWE-703: Improper Check or Handling of Exceptional Conditions  
  [https://cwe.mitre.org/data/definitions/703.html](https://cwe.mitre.org/data/definitions/703.html)

## 説明
捕捉されない例外はスマートコントラクトがエラーを適切に処理できない場合に発生し、意図しない動作、資金の喪失、サービス拒否 (DoS) につながります。Solidity では、`call()`, `delegatecall()`, `send()` などの低レベルの呼び出しは、自動的に元に戻るのではなくブール値を返すため、明示的なチェックが必要です。戻り値を無視したり、`require()` の代わりに `assert()` を使用すると、攻撃者が悪用できる脆弱性につながる可能性があります。

捕捉されない例外でのよくある問題:
- `call()`, `delegatecall()`, `send()` の戻り値を無視する
- チェックされていない外部コントラクトの呼び出し
- `require()` の代わりに `assert()` の誤った使用
- `try/catch` ブロックでエラーを捕捉できない

## 対策
- **戻り値をチェックする:** 低レベル呼び出しでは常に戻り値を検証し、実行が成功したことを確認します。
- **入力バリデーションに `require()` を使用する:** ユーザー入力のチェックに `require()` を使用し、不変条件にのみ `assert()` を使用します。
- **Try/Catch を実装する:** 外部コントラクトとやり取りする際に `try/catch` を使用し、サイレントな失敗を防ぎます。
- **安全なライブラリを使用する:** 安全なトークン転送には OpenZeppelin の `SafeERC20` を利用します。
- **外部依存関係を最小限に抑える:** 必要がない限り、外部コントラクトの動作に依存することを避けます。

## 事例

### 事例 1: 捕捉されない例外:

```solidity
pragma solidity ^0.8.0;

contract UncheckedCall {
    function execute(address target, bytes memory data) public payable {
        target.call{value: msg.value}(data); // ❌ No success check
    }
}
```

In the above example, `call()` returns a boolean indicating success, but it is ignored, which can lead to silent failures.

### 事例外を適切に処理するようにリファクタリングしたもの:

```solidity
pragma solidity ^0.8.0;

contract SafeCall {
    function execute(address target, bytes memory data) public payable {
        (bool success, bytes memory returnData) = target.call{value: msg.value}(data);
        require(success, "Call execution failed"); // ✅ Handle failure
    }
}
```

In this improved version, the contract checks whether `call()` was successful, preventing execution from continuing on failure.


### 事例 2: バッドプラクティス (require() ではなく assert() を使用している)

- Problem: Using assert() for input validation instead of require().

```solidity
pragma solidity ^0.8.0;

contract WrongAssertion {
    function withdraw(uint256 amount) public {
        assert(amount > 0); // ❌ Assert should not be used for input validation
        // Withdraw logic here
    }
}
```

Why is this vulnerable?
- `assert()` is meant for invariants, not input validation.
- If `assert()` fails, it consumes all remaining gas and does not return gas to the caller.

### ベタープラクティス (入力バリデーションの修正)

```solidity
pragma solidity ^0.8.0;

contract CorrectValidation {
    function withdraw(uint256 amount) public {
        require(amount > 0, "Invalid amount"); // ✅ Proper validation
        // Withdraw logic here
    }
}
```
- Now, a proper `require()` statement prevents execution if amount is invalid.

### 事例 3: バッドプラクティス (Try/Catch でエラーをキャッチしない)

- Problem: Not using try/catch for external contract calls.

```solidity
pragma solidity ^0.8.0;

interface ExternalContract {
    function riskyFunction() external;
}

contract NoErrorHandling {
    function callExternal(address contractAddr) public {
        ExternalContract(contractAddr).riskyFunction(); // ❌ No error handling
    }
}
```

Why is this vulnerable?
- If riskyFunction() fails, the transaction completely reverts.
- There is no way to recover or take alternative action.

### ベタープラクティス (外部呼び出しに Try/Catch を使用する)

```solidity
pragma solidity ^0.8.0;

interface ExternalContract {
    function riskyFunction() external;
}

contract SafeExternalCall {
    function callExternal(address contractAddr) public {
        try ExternalContract(contractAddr).riskyFunction() {
            // ✅ Success case
        } catch {
            revert("External call failed"); // ✅ Catch and handle failure
        }
    }
}
```
- Now, errors are properly caught, and alternative actions can be taken.
