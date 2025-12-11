---
title: blockhash の安全でない使用 (Insecure Use of blockhash)
id: SCWE-084
alias: insecure-use-of-blockhash
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
`blockhash` 関数はスマートコントラクトで乱数を生成するためにしばしば誤用されます。しかし、`blockhash` はパブリックに利用可能であり、マイナーによる影響を受ける可能性があり、信頼できず安全でない乱数源となります。

攻撃者は、ブロックに含まれるトランザクションを制御したり、トランザクションを並べ替えたり、不利なブロックを破棄して、`blockhash` を操作できます。これは予測可能なランダムの結果につながり、悪意のある人物がくじ、ゲーム、その他のランダム性に依存するメカニズムを悪用できるようになります。

**攻撃シナリオ**
- くじ操作: マイナーはトランザクションを保留または並べ替えて、勝利に有利な `blockhash` を確保できます。
- ゲーム悪用: ゲームの結果が `blockhash` に依存する場合、攻撃者は将来の結果を予測し、それに応じて賭けを行うことができます。

## 対策
乱数生成には `blockhash` に頼ってはいけません。Chainlink VRF やその他の信頼できるオラクルを使用するなど、より安全で、予測できない乱数源を使用します。

### 脆弱なコントラクトの例
```solidity
// Vulnerable contract using blockhash for randomness
pragma solidity ^0.8.0;

contract InsecureRandomness {
    function getRandomNumber(uint256 _blockNumber) public view returns (uint256) {
        return uint256(blockhash(_blockNumber)); // Predictable randomness
    }
}
```
**なぜこれが安全でないのか？**
- マイナーはブロック生成を制御します - ブロックを並べ替えたり破棄して `blockhash` を操作できます。
- 予測可能性 - 攻撃者は過去のブロック番号に対してこの関数を呼び出し、乱数を推測できます。


### 修正したコントラクトの例 - Chainlink VRF を使用した安全な乱数生成

```solidity
pragma solidity ^0.8.0;

import "@chainlink/contracts/src/v0.8/VRFConsumerBase.sol";

contract SecureLottery is VRFConsumerBase {
    address[] public players;
    bytes32 internal keyHash;
    uint256 internal fee;
    uint256 public randomResult;

    constructor()
        VRFConsumerBase(
            0x514910771AF9Ca656af840dff83E8264EcF986CA, // Chainlink VRF Coordinator
            0x514910771AF9Ca656af840dff83E8264EcF986CA // LINK Token address
        )
    {
        keyHash = 0x6c3699283bda56ad74f6b855546325b68d482e983852a617d11109b44b8aab01; 
        fee = 0.1 * 10**18; // 0.1 LINK (varies by network)
    }

    function enter() public payable {
        require(msg.value > 0.1 ether, "Minimum ETH required");
        players.push(msg.sender);
    }

    function requestRandomWinner() public returns (bytes32 requestId) {
        require(players.length > 0, "No players joined");
        require(LINK.balanceOf(address(this)) >= fee, "Not enough LINK");
        return requestRandomness(keyHash, fee);
    }

    function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
        uint256 index = randomness % players.length;
        payable(players[index]).transfer(address(this).balance);
    }
}
```

**なぜこれが安全なのか？**
- `Chainlink VRF` (Verifiable Random Function) を使用しています。これは予測不可能で改竄防止のある乱数を提供します。
- マイナーは乱数を操作できません。検証可能な外部ソースから導出されるためです。
- プレイヤーは参加前に結果を予測できません。
