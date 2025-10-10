---
title: スマートコントラクトのチェックされていない外部呼び出し (Unchecked External Calls in Smart Contracts)
---
### **説明**

チェックされていない外部呼び出しは、スマートコントラクトが呼び出しの結果を検証せずに別のコントラクトやアドレスへの外部呼び出しを行う場合に発生します。Ethereum では、外部呼び出しがサイレントに失敗し、呼び出し元のコントラクトが誤って呼び出しが成功したかのように処理を進める可能性があります。これは状態の不整合や潜在的な悪用につながります。この問題は delegatecall, send, call などの関数において、結果を明示的にチェックする必要があるため、特に危険です。

### **例: 適切なアクセス制御のないコード**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.4.24;

contract Proxy {
    address public owner;

    constructor() public {
        owner = msg.sender;
    }

    function forward(address callee, bytes _data) public {
        require(callee.delegatecall(_data)); // Unchecked external call vulnerability
    }
}
```
### **影響**

- 外部呼び出しが失敗し、その結果がチェックされていない場合、コントラクトは誤った仮定に基づいて処理を進める可能性があり、資金の損失やその他の予期しない動作につながる可能性があります。
- 検証されていない外部呼び出しはコントラクトの状態の誤った更新につながる可能性があり、エクスプロイトや論理的な不整合に対して脆弱になります。
- 攻撃者はこのような脆弱性を不正操作して、悪意のあるコードを実行したり、資金を複数回引き落としできます。


### **対策**


- Ether を転送する際は send() ではなく transfer() などのより安全なメソッドを使用します。transfer() 関数は、呼び出しが失敗した場合、自動的に元に戻ります。
- call や delegatecall などの低レベル関数では、常に戻り値をチェックし、失敗を適切に処理します。
- 信頼できないコントラクトとのやり取りを制限し、重要な操作を実行する前に堅牢なバリデーションを実施します。
