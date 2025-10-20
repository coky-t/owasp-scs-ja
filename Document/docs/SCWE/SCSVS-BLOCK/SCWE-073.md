---
title: ハードコードされたガス量でのメッセージ呼び出し (Message Call with Hardcoded Gas Amount)
id: SCWE-073
alias: message-call-with-harcoded-gas
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-1]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)

## 説明
Solidity では、ガス値をハードコードした外部コントラクトを呼び出すと、ガス不足や悪意のあるコントラクトがガス消費量を不正操作するなど、さまざまな問題につながる可能性があります。ガス量をハードコードすることは柔軟性に欠け、リソース枯渇や、ガス制限が操作に不十分な場合にトランザクションが失敗する可能性があります。

## 対策
ガス量をハードコードするのではなく、ガスを自動的に決定したり、トランザクションのニーズに応じてガスを動的に調整できるほうがより適切です。これは、不要なリソース消費を避けながら、トランザクションが正常に完了できるようにします。

### 脆弱なコントラクトの例
```solidity
contract Example {
    address public target;

    function callTarget() public {
        // Hardcoding the gas value for the message call
        target.call{gas: 100000}("");  // Vulnerable to resource consumption issues
    }
}
```

### 修正したコントラクトの例
```solidity
contract Example {
    address public target;

    function callTarget() public {
        // Let Solidity handle gas consumption dynamically
        target.call("");  // Gas amount handled by the EVM dynamically
    }
}
```
