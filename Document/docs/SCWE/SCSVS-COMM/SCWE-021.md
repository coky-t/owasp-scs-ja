---
title: 安全でないデータ転送 (Unsecured Data Transmission)
id: SCWE-021
alias: unsecured-data-transmission
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [319]
status: new
---

## 関連
- CWE-319: Cleartext Transmission of Sensitive Information
  [CWE-319 Link](https://cwe.mitre.org/data/definitions/319.html)

## 説明
Unsecured data transmission refers to the transmission of sensitive information, such as private keys or user data, without encryption. This can lead to:
- Interception of sensitive data by malicious actors.
- Exploitation of vulnerabilities in the contract.
- Loss of funds or data.

## 対策
- **Use encryption:** Encrypt sensitive data before transmission.
- **Leverage secure protocols:** Use HTTPS or other secure communication protocols.
- **Avoid transmitting sensitive data:** Minimize the transmission of sensitive data whenever possible.

## 事例
- **Unsecured Data Transmission**
    ```solidity
    pragma solidity ^0.8.0;

    contract UnsecuredData {
        function transmitData(bytes memory data) public {
            // Transmit data without encryption
        }
    }
    ```

- **Secured Data Transmission**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecuredData {
        function transmitData(bytes memory encryptedData) public {
            // Transmit encrypted data
        }
    }
    ```

---
