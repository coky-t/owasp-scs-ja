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
Uncaught exceptions occur when a smart contract fails to properly handle errors, leading to unintended behaviors, lost funds, or denial of service (DoS). In Solidity, low-level calls such as `call()`, `delegatecall()`, and `send()` return a boolean instead of reverting automatically, requiring explicit checks. Ignoring return values or using `assert()` instead of `require()` can result in vulnerabilities that attackers can exploit.

Common issues with uncaught exceptions:
- Ignoring return values of `call()`, `delegatecall()`, `send()`
- Unchecked external contract calls
- Incorrect use of `assert()` instead of `require()`
- Failure to catch errors in `try/catch` blocks

## 対策
- **Check Return Values:** Always verify the return values of low-level calls to ensure execution succeeded.
- **Use `require()` for Input Validation:** `require()` should be used for checking user input, while `assert()` should only be used for invariants.
- **Implement Try/Catch:** Use `try/catch` when interacting with external contracts to prevent silent failures.
- **Use Safe Libraries:** Utilize OpenZeppelin’s `SafeERC20` for secure token transfers.
- **Minimize External Dependencies:** Avoid relying on external contract behavior unless necessary.

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
