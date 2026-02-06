## OWASP スマートコントラクト脆弱性タイプ一覧 日本語版

- [OWASP スマートコントラクト脆弱性タイプ一覧](Document/docs/SCWE/index.md)

### SCSVS-ARCH

- [SCWE-001](Document/docs/SCWE/SCSVS-ARCH/SCWE-001.md) 不適切なコントラクトアーキテクチャ (Improper Contract Architecture)
- [SCWE-002](Document/docs/SCWE/SCSVS-ARCH/SCWE-002.md) 過度なコントラクトの複雑さ (Excessive Contract Complexity)
- [SCWE-003](Document/docs/SCWE/SCSVS-ARCH/SCWE-003.md) モジュール性の欠如 (Lack of Modularity)
- [SCWE-004](Document/docs/SCWE/SCSVS-ARCH/SCWE-004.md) 捕捉されない例外 (Uncaught Exceptions)
- [SCWE-005](Document/docs/SCWE/SCSVS-ARCH/SCWE-005.md) 安全でないアップグレード可能なプロキシ設計 (Insecure Upgradeable Proxy Design)
- [SCWE-006](Document/docs/SCWE/SCSVS-ARCH/SCWE-006.md) 一貫性のない継承階層 (Inconsistent Inheritance Hierarchy)
- [SCWE-051](Document/docs/SCWE/SCSVS-ARCH/SCWE-051.md) コントラクトデプロイメントにおける CREATE2 の不適切な使用 (Improper Use of CREATE2 for Contract Deployment)
- [SCWE-052](Document/docs/SCWE/SCSVS-ARCH/SCWE-052.md) トランザクション順序の依存性 (Transaction Order Dependence)
- [SCWE-064](Document/docs/SCWE/SCSVS-ARCH/SCWE-064.md) 不正な継承順位 (Incorrect Inheritance Order)
- [SCWE-070](Document/docs/SCWE/SCSVS-ARCH/SCWE-070.md) 不正なコンストラクタ名 (Incorrect Constructor Name)
- [SCWE-071](Document/docs/SCWE/SCSVS-ARCH/SCWE-071.md) 初期化されていないストレージポインタ (Uninitialized Storage Pointer)
- [SCWE-080](Document/docs/SCWE/SCSVS-ARCH/SCWE-080.md) 不正な型変換 (Incorrect Type Conversion)
<!--
- [SCWE-098](Document/docs/SCWE/SCSVS-ARCH/SCWE-098.md) (Initialization Front-Running in Upgradeable Contracts)
- [SCWE-099](Document/docs/SCWE/SCSVS-ARCH/SCWE-099.md) (Storage Layout Collision on Upgrade)
- [SCWE-117](Document/docs/SCWE/SCSVS-ARCH/SCWE-117.md) (Proxy Implementation Selfdestruct Exposure)
- [SCWE-118](Document/docs/SCWE/SCSVS-ARCH/SCWE-118.md) (Unauthenticated Beacon Upgrade)
- [SCWE-119](Document/docs/SCWE/SCSVS-ARCH/SCWE-119.md) (Shared Proxy Admin and Logic Owner Key)
- [SCWE-145](Document/docs/SCWE/SCSVS-ARCH/SCWE-145.md) (Unvalidated Constructor Parameters)
- [SCWE-150](Document/docs/SCWE/SCSVS-ARCH/SCWE-150.md) (Storage Slot Collision When Upgrading Implementation)
-->

### SCSVS-CODE

- [SCWE-007](Document/docs/SCWE/SCSVS-CODE/SCWE-007.md) 未使用変数の存在 (Presence of Unused Variables)
- [SCWE-008](Document/docs/SCWE/SCSVS-CODE/SCWE-008.md) ハードコードされた定数 (Hardcoded Constants)
- [SCWE-009](Document/docs/SCWE/SCSVS-CODE/SCWE-009.md) 非推奨の変数と関数の使用 (Deprecated Variable and Function Usage)
- [SCWE-010](Document/docs/SCWE/SCSVS-CODE/SCWE-010.md) 変数と関数のシャドウイング (Shadowing Variables and Functions)
- [SCWE-011](Document/docs/SCWE/SCSVS-CODE/SCWE-011.md) 安全でない ABI エンコーディングとデコーディング (Insecure ABI Encoding and Decoding)
- [SCWE-013](Document/docs/SCWE/SCSVS-CODE/SCWE-013.md) 不正なパラメータ変更 (Unauthorized Parameter Changes)
- [SCWE-014](Document/docs/SCWE/SCSVS-CODE/SCWE-014.md) 緊急停止メカニズムの欠如 (Lack of Emergency Stop Mechanism)
- [SCWE-039](Document/docs/SCWE/SCSVS-CODE/SCWE-039.md) インラインアセンブリの安全でない使用 (Insecure Use of Inline Assembly)
- [SCWE-040](Document/docs/SCWE/SCSVS-CODE/SCWE-040.md) 不正なストレージパッキング (Incorrect Storage Packing)
- [SCWE-041](Document/docs/SCWE/SCSVS-CODE/SCWE-041.md) 安全でないダウンキャスト (Unsafe Downcasting)
- [SCWE-046](Document/docs/SCWE/SCSVS-CODE/SCWE-046.md) 再入攻撃 (Reentrancy Attacks)
- [SCWE-047](Document/docs/SCWE/SCSVS-CODE/SCWE-047.md) 整数オーバーフローとアンダーフロー (Integer Overflows and Underflows)
- [SCWE-048](Document/docs/SCWE/SCSVS-CODE/SCWE-048.md) チェックされない関数戻り値 (Unchecked Call Return Value)
- [SCWE-053](Document/docs/SCWE/SCSVS-CODE/SCWE-053.md) マッピングの不適切な削除 (Improper Deletion of Mappings)
- [SCWE-060](Document/docs/SCWE/SCSVS-CODE/SCWE-060.md) フローティングプラグマ (Floating Pragma)
- [SCWE-061](Document/docs/SCWE/SCSVS-CODE/SCWE-061.md) 古いコンパイラバージョン (Outdated Compiler Version)
- [SCWE-062](Document/docs/SCWE/SCSVS-CODE/SCWE-062.md) デッドコード (Dead Code)
- [SCWE-066](Document/docs/SCWE/SCSVS-CODE/SCWE-066.md) ビット演算の誤った処理 (Incorrect Handling of Bitwise Operations)
- [SCWE-067](Document/docs/SCWE/SCSVS-CODE/SCWE-067.md) アサートバイオレーション (Assert Violation)
- [SCWE-068](Document/docs/SCWE/SCSVS-CODE/SCWE-068.md) 状態変数のデフォルトの可視性 (State Variable Default Visibility)
- [SCWE-069](Document/docs/SCWE/SCSVS-CODE/SCWE-069.md) 状態変数のシャドウイング (Shadowing State Variables)
- [SCWE-072](Document/docs/SCWE/SCSVS-CODE/SCWE-072.md) 非推奨の Solidity 関数の使用 (Use of Deprecated Solidity Functions)
- [SCWE-076](Document/docs/SCWE/SCSVS-CODE/SCWE-076.md) Right-To-Left-Override 制御文字 (U+202E) (Right-To-Left-Override Control Character (U+202E))
- [SCWE-089](Document/docs/SCWE/SCSVS-CODE/SCWE-089.md) 脆弱で古いライブラリ (Vulnerable & Outdated Libraries)
- [SCWE-090](Document/docs/SCWE/SCSVS-CODE/SCWE-090.md) 自動トークンスワップでのスリッページ保護の欠如 (Missing Slippage Protection in Automated Token Swaps)
- [SCWE-091](Document/docs/SCWE/SCSVS-CODE/SCWE-091.md) トークン転送でのゼロ値チェックの欠如 (Lack of Zero Value Check in Token Transfers)
- [SCWE-092](Document/docs/SCWE/SCSVS-CODE/SCWE-092.md) プロキシコントラクトのコンストラクタでの初期化子無効化の欠如 (Missing Disable Initializer in Constructor for Proxy Contracts)
- [SCWE-093](Document/docs/SCWE/SCSVS-CODE/SCWE-093.md) 名前のない関数パラメータ (Unnamed Function Parameters)
- [SCWE-095](Document/docs/SCWE/SCSVS-CODE/SCWE-095.md) 宛先アドレスのサイズチェックの欠如 (Missing Destination Address Size Check)
<!--
- [SCWE-097](Document/docs/SCWE/SCSVS-CODE/SCWE-097.md) (Missing Explicit Function Visibility)
- [SCWE-120](Document/docs/SCWE/SCSVS-CODE/SCWE-120.md) (Missing Return Data Length Validation)
- [SCWE-121](Document/docs/SCWE/SCSVS-CODE/SCWE-121.md) (Swallowed Revert Reasons)
- [SCWE-122](Document/docs/SCWE/SCSVS-CODE/SCWE-122.md) (Calldata Length Not Validated Before Decode)
- [SCWE-123](Document/docs/SCWE/SCSVS-CODE/SCWE-123.md) (Corrupt Free Memory Pointer in Assembly)
- [SCWE-124](Document/docs/SCWE/SCSVS-CODE/SCWE-124.md) (Inconsistent Rounding Direction in Financial Math)
- [SCWE-137](Document/docs/SCWE/SCSVS-CODE/SCWE-137.md) (Read-Only Reentrancy via View Function State Staleness)
- [SCWE-141](Document/docs/SCWE/SCSVS-CODE/SCWE-141.md) (Lack of Deadline Validation in Time-Sensitive External Calls)
- [SCWE-143](Document/docs/SCWE/SCSVS-CODE/SCWE-143.md) (Critical Address Parameters Not Validated for Zero Address)
- [SCWE-144](Document/docs/SCWE/SCSVS-CODE/SCWE-144.md) (Bypassable Contract Existence Check via extcodesize)
- [SCWE-146](Document/docs/SCWE/SCSVS-CODE/SCWE-146.md) (Improper Use of try/catch Leading to Silent Failures)
- [SCWE-152](Document/docs/SCWE/SCSVS-CODE/SCWE-152.md) (Misuse of Custom Errors Leading to Information Leakage or Wrong Revert Behavior)
- [SCWE-154](Document/docs/SCWE/SCSVS-CODE/SCWE-154.md) (Calldata Decode Without Length Check)
-->

### SCSVS-GOV

- [SCWE-012](Document/docs/SCWE/SCSVS-GOV/SCWE-012.md) マルチシグガバナンスの欠如 (Lack of Multisig Governance)
- [SCWE-015](Document/docs/SCWE/SCSVS-GOV/SCWE-015.md) 不十分なガバナンス文書 (Poor Governance Documentation)
- [SCWE-037](Document/docs/SCWE/SCSVS-GOV/SCWE-037.md) フロントランニングに対する不十分な保護 (Insufficient Protection Against Front-Running)
- [SCWE-043](Document/docs/SCWE/SCSVS-GOV/SCWE-043.md) フォールバック関数の安全でない使用 (Insecure Use of Fallback Functions)
- [SCWE-044](Document/docs/SCWE/SCSVS-GOV/SCWE-044.md) ストレージの安全でない使用 (Insecure Use of Storage)
- [SCWE-075](Document/docs/SCWE/SCSVS-GOV/SCWE-075.md) 不正な Ether 残高追跡 (Incorrect Ether Balance Tracking)
- [SCWE-078](Document/docs/SCWE/SCSVS-GOV/SCWE-078.md) Ether 送金の不適切な取り扱い (Improper Handling of Ether Transfers)
- [SCWE-079](Document/docs/SCWE/SCSVS-GOV/SCWE-079.md) Transfer と Send の安全でない使用 (Insecure Use of Transfer and Send)
- [SCWE-081](Document/docs/SCWE/SCSVS-GOV/SCWE-081.md) Nonce の不適切な取り扱い (Improper Handling of Nonce)

### SCSVS-AUTH

- [SCWE-016](Document/docs/SCWE/SCSVS-AUTH/SCWE-016.md) 不十分な認可チェック (Insufficient Authorization Checks)
- [SCWE-017](Document/docs/SCWE/SCSVS-AUTH/SCWE-017.md) 特権ロールの管理ミス (Privileged Role Mismanagement)
- [SCWE-018](Document/docs/SCWE/SCSVS-AUTH/SCWE-018.md) tx.origin を使用した認可 (Use of tx.origin for Authorization)
- [SCWE-019](Document/docs/SCWE/SCSVS-AUTH/SCWE-019.md) 安全でない署名検証 (Insecure Signature Verification)
- [SCWE-020](Document/docs/SCWE/SCSVS-AUTH/SCWE-020.md) 時間ロック機能の欠如 (Absence of Time-Locked Functions)
- [SCWE-038](Document/docs/SCWE/SCSVS-AUTH/SCWE-038.md) Selfdestruct の安全でない使用 (Insecure Use of Selfdestruct)
- [SCWE-045](Document/docs/SCWE/SCSVS-AUTH/SCWE-045.md) Modifier の安全でない使用 (Insecure Use of Modifiers)
- [SCWE-049](Document/docs/SCWE/SCSVS-AUTH/SCWE-049.md) 保護されていない Ether の引き落とし (Unprotected Ether Withdrawal)
- [SCWE-050](Document/docs/SCWE/SCSVS-AUTH/SCWE-050.md) 保護されていない SELFDESTRUCT 命令 (Unprotected SELFDESTRUCT Instruction)
<!--
- [SCWE-105](Document/docs/SCWE/SCSVS-AUTH/SCWE-105.md) (Permit Signature Replay via Missing Domain Separator or Nonce)
- [SCWE-106](Document/docs/SCWE/SCSVS-AUTH/SCWE-106.md) (Unauthenticated Meta-Transactions)
- [SCWE-129](Document/docs/SCWE/SCSVS-AUTH/SCWE-129.md) (Single EOA Admin Without Rotation)
- [SCWE-139](Document/docs/SCWE/SCSVS-AUTH/SCWE-139.md) (Single-Step Ownership Transfer Without Confirmation)
- [SCWE-147](Document/docs/SCWE/SCSVS-AUTH/SCWE-147.md) (Permit or Meta-Transaction Signatures Without Expiration)
- [SCWE-155](Document/docs/SCWE/SCSVS-AUTH/SCWE-155.md) (Single Point of Failure in Administrative Key Management)
-->

### SCSVS-COMM

- [SCWE-021](Document/docs/SCWE/SCSVS-COMM/SCWE-021.md) 安全でないデータ転送 (Unsecured Data Transmission)
- [SCWE-022](Document/docs/SCWE/SCSVS-COMM/SCWE-022.md) メッセージリプレイの脆弱性 (Message Replay Vulnerabilities)
- [SCWE-023](Document/docs/SCWE/SCSVS-COMM/SCWE-023.md) 通信の真正性の欠如 (Lack of Communication Authenticity)
- [SCWE-035](Document/docs/SCWE/SCSVS-COMM/SCWE-035.md) 安全でない Delegatecall の使用 (Insecure Delegatecall Usage)
- [SCWE-042](Document/docs/SCWE/SCSVS-COMM/SCWE-042.md) 外部呼び出しの安全でない使用 (Insecure Use of External Calls)
- [SCWE-063](Document/docs/SCWE/SCSVS-COMM/SCWE-063.md) 安全でないイベント発行 (Insecure Event Emission)
<!--
- [SCWE-103](Document/docs/SCWE/SCSVS-COMM/SCWE-103.md) (ERC20 Approval Double-Spend (Allowance Race))
- [SCWE-104](Document/docs/SCWE/SCSVS-COMM/SCWE-104.md) (Unprotected ERC777 Token Hooks)
- [SCWE-107](Document/docs/SCWE/SCSVS-COMM/SCWE-107.md) (Missing Chain ID Validation in Cross-Chain Messages)
- [SCWE-108](Document/docs/SCWE/SCSVS-COMM/SCWE-108.md) (Unverified Cross-Chain Message Proofs)
- [SCWE-128](Document/docs/SCWE/SCSVS-COMM/SCWE-128.md) (Insecure Multicall Context Forwarding)
- [SCWE-134](Document/docs/SCWE/SCSVS-COMM/SCWE-134.md) (Low-Level Call to Non-Contract Address)
- [SCWE-138](Document/docs/SCWE/SCSVS-COMM/SCWE-138.md) (Reentrancy via ERC721/ERC1155 Safe Transfer Callbacks)
- [SCWE-149](Document/docs/SCWE/SCSVS-COMM/SCWE-149.md) (Transfers to Addresses That Cannot Receive Funds)
-->

### SCSVS-CRYPTO

- [SCWE-025](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-025.md) 不適切な暗号鍵管理 (Improper Cryptographic Key Management)
- [SCWE-026](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-026.md) 不十分なハッシュ検証 (Insufficient Hash Verification)
- [SCWE-027](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-027.md) 脆弱な暗号アルゴリズム (Vulnerable Cryptographic Algorithms)
- [SCWE-054](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-054.md) 署名変更可能性 (Signature Malleability)
- [SCWE-055](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-055.md) 署名リプレイ攻撃に対する保護の欠如 (Missing Protection against Signature Replay Attacks)
- [SCWE-056](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-056.md) 適切な署名検証の欠如 (Lack of Proper Signature Verification)
- [SCWE-074](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-074.md) 複数の可変長引数によるハッシュ衝突 (Hash Collisions with Multiple Variable Length Arguments)
- [SCWE-084](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-084.md) blockhash の安全でない使用 (Insecure Use of blockhash)
<!--
- [SCWE-114](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-114.md) (ECDSA Nonce Reuse)
- [SCWE-115](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-115.md) (Weak VRF Parameterization or Callback Validation)
- [SCWE-131](Document/docs/SCWE/SCSVS-CRYPTO/SCWE-131.md) (Missing Domain Separation in Aggregate Signatures)
-->

### SCSVS-ORACLE

- [SCWE-028](Document/docs/SCWE/SCSVS-ORACLE/SCWE-028.md) 価格オラクル操作 (Price Oracle Manipulation)
- [SCWE-029](Document/docs/SCWE/SCSVS-ORACLE/SCWE-029.md) 分散型オラクルソースの欠如 (Lack of Decentralized Oracle Sources)
- [SCWE-030](Document/docs/SCWE/SCSVS-ORACLE/SCWE-030.md) 安全でないオラクルデータ更新 (Insecure Oracle Data Updates)
- [SCWE-085](Document/docs/SCWE/SCSVS-ORACLE/SCWE-085.md) オラクルの最小/最大価格帯の検証なしでの誤用 (Misuse of Oracle Min/Max Price Band Without Validation)
- [SCWE-086](Document/docs/SCWE/SCSVS-ORACLE/SCWE-086.md) オラクルレスポンスフィールドの検証の欠如 (古いデータまたは不完全なデータ) (Missing Validation of Oracle Response Fields (Stale or Incomplete Data))
- [SCWE-088](Document/docs/SCWE/SCSVS-ORACLE/SCWE-088.md) 価格ベースの計算における不適切な Decimal 正規化 (Improper Decimal Normalization in Price-Based Calculations)
<!--
- [SCWE-112](Document/docs/SCWE/SCSVS-ORACLE/SCWE-112.md) (Reliance on Low-Liquidity Spot Prices)
- [SCWE-113](Document/docs/SCWE/SCSVS-ORACLE/SCWE-113.md) (Insufficient TWAP Window or Single Observation)
- [SCWE-130](Document/docs/SCWE/SCSVS-ORACLE/SCWE-130.md) (Admin-Write Oracle Without Delay)
-->

### SCSVS-BLOCK

- [SCWE-024](Document/docs/SCWE/SCSVS-BLOCK/SCWE-024.md) 弱いランダム性ソース (Weak Randomness Sources)
- [SCWE-031](Document/docs/SCWE/SCSVS-BLOCK/SCWE-031.md) ブロック変数の安全でない使用 (Insecure use of Block Variables)
- [SCWE-065](Document/docs/SCWE/SCSVS-BLOCK/SCWE-065.md) 時間を代理としたブロック値 (Block Values as a Proxy for Time)
- [SCWE-073](Document/docs/SCWE/SCSVS-BLOCK/SCWE-073.md) ハードコードされたガス量でのメッセージ呼び出し (Message Call with Hardcoded Gas Amount)
<!--
- [SCWE-127](Document/docs/SCWE/SCSVS-BLOCK/SCWE-127.md) (EIP-1559 Basefee Assumptions)
- [SCWE-153](Document/docs/SCWE/SCSVS-BLOCK/SCWE-153.md) (Reliance on block.prevrandao for High-Value Randomness)
-->

### SCSVS-BRIDGE

- [SCWE-032](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-032.md) ブロックガス制限への依存 (Dependency on Block Gas Limit)
- [SCWE-033](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-033.md) チェーン分割リスク (Chain Split Risks)
- [SCWE-034](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-034.md) 安全でないクロスチェーンメッセージング (Insecure Cross-Chain Messaging)
- [SCWE-087](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-087.md) クロスチェーンメッセージングにおけるペイロードサイズ検証の欠如 (サービス拒否/資金の滞留) (Missing Payload Size Validation in Cross-Chain Messaging (Denial of Service/Stuck Funds))
- [SCWE-094](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-094.md) LayerZero メッセージ送信における不十分なガス制限バリデーション (Insufficient Gas Limit Validation in LayerZero Message Sending)
- [SCWE-096](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-096.md) クロスチェーン NFT 引き落とし時のトークンバーンの欠如 (Missing Token Burn During Cross-Chain NFT Withdrawal)
<!--
- [SCWE-132](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-132.md) (Mismatched Token Decimals in Bridge Mint/Burn)
- [SCWE-133](Document/docs/SCWE/SCSVS-BRIDGE/SCWE-133.md) (Missing Replay Nonce per Bridge Lane)
-->

### SCSVS-DEFI

- [SCWE-036](Document/docs/SCWE/SCSVS-DEFI/SCWE-036.md) 不適切なガス制限処理 (Inadequate Gas Limit Handling)
- [SCWE-058](Document/docs/SCWE/SCSVS-DEFI/SCWE-058.md) ブロックガス制限での DoS (DoS with Block Gas Limit)
- [SCWE-059](Document/docs/SCWE/SCSVS-DEFI/SCWE-059.md) 不十分なガスブリーフィング (Insufficient Gas Griefing)
- [SCWE-077](Document/docs/SCWE/SCSVS-DEFI/SCWE-077.md) レート制限の欠如 (Lack of Rate Limiting)
- [SCWE-082](Document/docs/SCWE/SCSVS-DEFI/SCWE-082.md) 適切なガス管理の欠如 (Lack of Proper Gas Management)
<!--
- [SCWE-109](Document/docs/SCWE/SCSVS-DEFI/SCWE-109.md) (Unbounded Loops on Untrusted Input)
- [SCWE-116](Document/docs/SCWE/SCSVS-DEFI/SCWE-116.md) (Missing Supply Cap Enforcement)
- [SCWE-125](Document/docs/SCWE/SCSVS-DEFI/SCWE-125.md) (Missing Post-Operation Health Check)
- [SCWE-126](Document/docs/SCWE/SCSVS-DEFI/SCWE-126.md) (Unbounded Withdrawal Queue Growth)
- [SCWE-148](Document/docs/SCWE/SCSVS-DEFI/SCWE-148.md) (Gas Exhaustion via Unbounded Loops with External Calls)
- [SCWE-151](Document/docs/SCWE/SCSVS-DEFI/SCWE-151.md) (Add/Remove Liquidity Without Minimum Output Validation)
-->

### SCSVS-COMP

- [SCWE-057](Document/docs/SCWE/SCSVS-COMP/SCWE-057.md) 任意のストレージ位置への書き込み (Write to Arbitrary Storage Location)
- [SCWE-083](Document/docs/SCWE/SCSVS-COMP/SCWE-083.md) エッジケースの処理の失敗 (Failure to Handle Edge Cases)
<!--
- [SCWE-110](Document/docs/SCWE/SCSVS-COMP/SCWE-110.md) (Fee-On-Transfer Token Misaccounting)
- [SCWE-111](Document/docs/SCWE/SCSVS-COMP/SCWE-111.md) (Rebase Token Balance Drift)
- [SCWE-135](Document/docs/SCWE/SCSVS-COMP/SCWE-135.md) (ERC4626 Share Inflation via Donations)
-->
