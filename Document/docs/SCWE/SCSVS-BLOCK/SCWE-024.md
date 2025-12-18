---
title: 弱いランダム性ソース (Weak Randomness Sources)
id: SCWE-024
alias: weak-randomness-sources
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-1]
  cwe: [336]
status: new
---

## 関連
- CWE-336: Predictable Random Number Generator  
  [CWE-336 リンク](https://cwe.mitre.org/data/definitions/336.html)

## 説明
弱いランダム性ソースは、ブロックタイムスタンプやブロックハッシュなど、予測可能または安全でないランダム性ソースの使用を指します。これは以下につながる可能性があります。
- コントラクトのロジックの悪用。
- 資金やデータの喪失。
- コントラクトのセキュリティに対する信頼の低下。

## 対策
- **安全なランダム性を使用する:** Chainlink VRF などの安全なランダム性ソースを活用します。
- **ブロック変数を避ける:** ランダム性にはブロックタイムスタンプやハッシュに依存してはいけません。
- **徹底的にテストする:** 広範なテストを実施して、ランダム性が安全であることを確認します。

## 事例
- **弱いランダム性**
    ```solidity
    pragma solidity ^0.8.0;

    contract WeakRandomness {
        function generateRandomNumber() public view returns (uint) {
            return uint(keccak256(abi.encodePacked(block.timestamp, block.difficulty))); // Insecure randomness
        }
    }
    ```

- **安全なランダム性**
    ```solidity
    pragma solidity ^0.8.0;

    import "@chainlink/contracts/src/v0.8/VRFConsumerBase.sol";

    contract SecureRandomness is VRFConsumerBase {
        bytes32 internal keyHash;
        uint256 internal fee;
        uint256 public randomResult;

        constructor(address vrfCoordinator, address linkToken, bytes32 _keyHash, uint256 _fee)
            VRFConsumerBase(vrfCoordinator, linkToken) {
            keyHash = _keyHash;
            fee = _fee;
        }

        function getRandomNumber() public returns (bytes32 requestId) {
            require(LINK.balanceOf(address(this)) >= fee, "Not enough LINK");
            return requestRandomness(keyHash, fee);
        }

        function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
            randomResult = randomness;
        }
    }
    ```

---
