---
title: 任意のストレージ位置への書き込み (Write to Arbitrary Storage Location)
id: SCWE-057
alias: write-arbitrary-storage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))


## 説明
コントラクトがストレージ変数とやり取りする前に入力を適切に検証できない場合、任意のストレージ位置への書き込みが発生する可能性があります。攻撃者はこの脆弱性を悪用して機密性の高いストレージスロットを上書きし、意図しないコントラクトの動作、状態の不正操作、資金の損失につながる可能性があります。

## 対策
この脆弱性を緩和するには、ストレージ関連の操作へのすべての入力が適切に検証されていることを確認します。外部ユーザーが任意のストレージ位置を指定できないようにします。アクセス制御メカニズムを使用して、機密性の高い状態変数を変更できるユーザーを制限し、信頼できるユーザーまたはコントラクト関数のみが重要なストレージ位置への書き込みできるようにします。

### 脆弱なコントラクトの例
```solidity
contract ArbitraryStorageWrite {
    uint256 public balance;

    function setStorage(uint256 storageLocation, uint256 value) public {
        assembly {
            sstore(storageLocation, value)  // Writing to arbitrary storage location
        }
    }
}
```

### 修正したコントラクトの例
```solidity
contract SecureStorageWrite {
    uint256 public balance;
    address public owner;

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function setBalance(uint256 value) public onlyOwner {
        balance = value;  // Only the owner can modify the balance
    }
}
```
