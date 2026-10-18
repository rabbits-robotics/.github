# リポジトリ管理方針 検討メモ（2026-10）

gamma 開発に合わせて Rabbits のリポジトリ運用を見直した際の検討メモ。
最終的には組織の handbook リポジトリへ移す。それまでの仮置き場として gamma_main に置く。

---

## 1. 決まったこと

| 項目 | 決定 |
|---|---|
| リポジトリの切り方 | 機体ごとに切る（beta / gamma …）。命名は `機体名_役割`（例: `gamma_main`、`gamma_turret_pcb`） |
| 機体管理リポジトリ | 機体ごとに `gamma` のような管理リポジトリを作り、機体全体の構成・基板・配線・リンク集を置く |
| ファームリポジトリ | `gamma_main` は gamma_turret 基板（ESP32-P4）のファーム。新規作成だが資産は rabcl に寄せる |
| 共通ライブラリ | rabcl を共通ライブラリとして育て続ける。ファームは submodule、ROS 2 は `.repos` で参照 |
| ROS 2 ワークスペース | attracts_ws は維持。機体ごとに `.repos` を分け、`vcs export --exact` でコミット固定する |
| 機体固有の設定 | launch / パラメータ / URDF は attracts_description に集約 |
| 開発環境（ファーム） | VS Code + PlatformIO 拡張機能で完結させる |
| 基板データ | 3 枚とも生基板到着・部品実装中。GitHub 未登録。後でプロジェクトごとにリポジトリ化 |
| メカ CAD | 保留。GitHub 管理は難しそう（下記 5 章） |
| 運用ルールの置き場 | 組織 Wiki ではなくドキュメント専用リポジトリ（handbook）（下記 6 章） |

---

## 2. gamma_main README の現状

ブランチ `docs/readme-spec`（kota0314/gamma_main）に push 済み。
各項目に ✅ 確定・実装済み / 🔧 実装中 / 💭 構想段階 のステータスを付ける方針。
章立ては beta_main の README（システム構成 → 基板詳細 → 前世代からの変更点 → 共通仕様 → 依存ライブラリ → ビルド方法）を下敷きにしている。

### 反映済み

- 基板 3 枚の構成、電源系統、基板間接続
- 車体: DM2325 × 4（CAN2、オムニ 4 輪、`rabcl::OmniDrive`）
- 脚: DM4310 × 4（CAN2、各車輪の持ち上げ機構。アクティブダンパ・車高調整）
- 射出ローラー: ESC × 2、50Hz サーボパルス、待機 1000µs / 射出 1200µs、起動時キャリブレーション 2000µs → 1000µs 各 3 秒（beta_main の実装値）
- rabcl をサブモジュールで継続。gamma で必要になる追加（DM4310、ST3215、BNO055 UART、ESP32 向け HAL 層）を列挙
- ビルドは PlatformIO + VS Code 拡張
- beta でもスリップリングは使用していた（変更点表を修正済み）

### 未決（TODO のまま）

| 項目 | 状態 |
|---|---|
| Jetson との通信プロトコル | rabcl の 28B 指令 / 112B フィードバックを引き継ぐか、Roll 軸・脚に合わせて拡張 or 新規定義か。別途相談 |
| CAN ID 一覧 | 首 3 軸、車輪 4 台、脚 4 台の割り当て。後で |
| 脚の制御モード | DM4310 の MIT モード想定。IMU やモータ FB 値を使ったトルク制御も候補。要相談 |
| 制御周期・CAN ビットレート | 未記入（beta は制御 100Hz、CAN TX 1500Hz、CAN 1Mbps） |
| PlatformIO の platform / framework | ESP32-P4 対応の platform 選定中。申し送りの `CONFIG_FREERTOS_UNICORE` は ESP-IDF の sdkconfig 項目なので IDF 前提に見える |
| 「ファームウェア申し送り」の見出し | 基板設計側からファーム側への制約事項。見出しは「ハード設計由来の制約」などに変える案 |

---

## 3. gamma_main README の分割案

管理リポジトリ `gamma` ができてから実施する。先に削ると情報が宙に浮く。

### gamma_main に残す

| 節 | 扱い |
|---|---|
| 冒頭の概要 | 1 段落に圧縮し、機体全体は `gamma` へのリンク |
| ステータス凡例 | そのまま |
| gamma_turret の I/O 表 | 「ファームが触るペリフェラル」として残す。基板状態の行は削除 |
| Jetson からの指令 | そのまま |
| アクチュエータ制御（脚の機能表含む） | そのまま |
| CAN ID 一覧 | そのまま |
| ファームウェア申し送り | 見出しを変えて残す |
| 共通仕様 | そのまま |
| 依存ライブラリ、ビルド方法 | そのまま |

### `gamma` へ移す

| 節 | 理由 |
|---|---|
| システム構成・データフローの全体図 | 機体全体の話。gamma_main には turret 視点の簡略図だけ残す |
| 基板間接続 | ハード |
| 電源系統 | ハード |
| gamma_neck / gamma_chassis（コネクタ挿し違い注意含む） | 受動基板はファームと無関係 |
| beta からの変更点 | 機体全体の比較。ファーム関連の行だけ gamma_main に短く残す |

### 手順

1. 組織に `gamma` リポジトリを作る（空で可）
2. 下の雛形に gamma_main の該当節を流し込んだ README を作成し、レビュー
3. 確認後に gamma_main 側を削り、両方を push

---

## 4. `gamma` リポジトリ README 雛形

```markdown
# gamma

ATTRACTS 向け対戦ロボット gamma（beta の次世代機）の機体管理リポジトリ。
機体全体の構成、基板、配線、関連リポジトリへの入口をここに集約する。

> ステータス凡例: ✅ 確定・実装済み / 🔧 実装中 / 💭 構想段階

## 関連リポジトリ

| 役割 | リポジトリ | 備考 |
|---|---|---|
| 砲塔ファーム（ESP32-P4） | gamma_main | turret 基板の制御 |
| 共通ライブラリ | rabcl | ファームと ROS 2 で共用。タグで固定 |
| ROS 2 ワークスペース | attracts_ws | `gamma.repos` を使う |
| 機体固有設定（launch / パラメータ / URDF） | attracts_description | |
| 基板 turret / neck / chassis | （リポジトリ化予定） | KiCad |
| メカ CAD | 未定 | |

## 環境構築（Jetson 側）

    git clone <attracts_ws>
    cd attracts_ws
    vcs import src < gamma.repos
    colcon build

`gamma.repos` は `vcs export --exact src` で生成したコミット固定版。
更新は機体のマイルストーンごとに行い、rabcl は gamma_main の submodule と同じコミットを指す。

## システム構成・データフロー
（gamma_main README の全体図をここへ）

## 基板構成

| 基板 | 役割 | 状態 |
|---|---|---|
| gamma_turret | 制御基板。Stamp-P4、CAN×2、ESC PWM×2、ST3215 UART、Jetson UART、BNO055 | 部品実装中 🔧 |
| gamma_neck | 受動分配。スリップリング受け口、24V / CAN 分配 | 部品実装中 🔧 |
| gamma_chassis | 受動分配ハブ。24V / CAN2 分配 | 部品実装中 🔧 |

### 基板間接続
（gamma_main README から移動）

### 電源系統
（gamma_main README から移動）

### 組立時の注意
（neck のコネクタ挿し違い注意をここへ）

## アクチュエータ一覧

| 部位 | モータ | バス | 状態 |
|---|---|---|---|
| 首 Yaw | MHF5005（LK） | CAN1 | 💭 |
| 首 Pitch / Roll | DM3507 × 2（DAMIAO） | CAN1 | 💭 |
| 車輪 | DM2325 × 4、オムニ | CAN2 | 💭 |
| 脚 | DM4310 × 4、アクティブダンパ・車高調整 | CAN2 | 💭 |
| 射出 | ESC × 2 | PWM | 💭 |
| 装填 | ST3215 | 半二重 UART | 💭 |

## beta からの変更点
（gamma_main README から移動）

## 設計メモ
（「gamma 基板セット まとめ」などの設計時の判断を日付付きで残す）
```

---

## 5. attracts_ws の運用

### 現状

- vcstool の `.repos`（`default_package_list.repos` / `ci.repos`）でパッケージを集めるメタワークスペース
- 対象: attracts_bridge, attracts_description, attracts_interface, attracts_msgs, rabcl, rabcl_ros2
- CI（ros:jazzy-ros-base）で `vcs import` → `rosdep install` → `colcon build` → `colcon test`

### 課題と対応

| 課題 | 対応 |
|---|---|
| 全パッケージが `version: main` で固定されていない。gamma 向けに attracts_msgs を変えると beta 環境が組めなくなる | 機体ごとに `beta.repos` / `gamma.repos` を作り、`vcs export --exact src > gamma.repos` でコミット固定。マイルストーンごとに更新（採用） |
| rabcl がファーム（submodule）と ROS 2（`.repos`）の 2 経路で参照され、同じコミットを指す保証がない | rabcl にタグを打ち、submodule と `.repos` の両方でそのタグ（コミット）を指す |
| 機体固有の launch / パラメータ / URDF の置き場が未定 | attracts_description に集約（採用） |
| README の clone URL が `robot-trainee/attracts_ws` のまま。環境構築手順が Raspberry Pi 5 前提 | URL を組織に直し、Jetson Orin Nano 向け手順を追記 |

---

## 6. メカ CAD の管理（保留）

GitHub で管理するなら Git LFS が前提になるが、現状は見送り。検討時のメモ:

- Fusion 360 / SolidWorks などのネイティブファイルはバイナリ。通常の git に入れるとリポジトリが肥大化する。LFS なら clone は軽い
- GitHub LFS の無料枠は保存 10GB、転送 10GB/月。アセンブリを頻繁にコミットすると転送枠を使い切る。Data pack 購入か、節目ごとのコミット運用が要る
- CAD はマージ不能。LFS のファイルロックで「編集中は他の人が触れない」運用になり、ブランチ並行作業とは相性が悪い
- レビュー用に STEP / STL / PNG を同じコミットで出力しておくと CAD なしで参照できる
- Onshape なら Onshape 側にバージョン管理があるので、GitHub には STEP とリンクだけ置くのが現実的
- 置き場は管理リポジトリと分けて `gamma_mech` のようにする（LFS 転送量がドキュメントの clone に影響しないため）

---

## 7. 運用ルールの置き場（handbook）

組織の Wiki ではなく、Markdown のドキュメント専用リポジトリを作る。

### 比較

| 置き場 | 向き | 弱点 |
|---|---|---|
| リポジトリの Wiki | 手軽 | リポジトリ単位でしか持てず組織全体の Wiki は無い。別 git で PR レビューが効かず検索も弱い |
| `.github` リポジトリの profile README | 組織トップに表示される入口 | 1 ページだけ |
| GitHub Discussions | 議論のログ | 決定事項が流れて埋もれる |
| ドキュメント専用リポジトリ | PR でレビューして履歴が残る。全リポジトリから同じリンクで参照できる | 書くときに clone か Web エディタが要る |

### 提案する構成

1. `rabbits-robotics/handbook` を作り、以下を Markdown で置く
   - `repositories.md`: リポジトリの切り方と命名、新しい機体を作るときの手順
   - `workspace.md`: attracts_ws の運用。機体ごとの `.repos`、`vcs export --exact`、rabcl のタグを submodule と `.repos` で揃えること
   - `readme-style.md`: README の章立てとステータス記号の凡例
   - `hardware.md`: 基板リポジトリと CAD の扱い（CAD は保留と明記）
2. `.github` リポジトリの profile README に handbook への 1 行リンク
3. 機体リポジトリのテンプレート: 4 章の雛形を `robot-template` としてテンプレートリポジトリにし、「Use this template」で揃える

### GitHub 設定に落とすもの

- main へのブランチ保護と PR 必須
- CODEOWNERS でレビュアー指定
- attracts_ws の CI で各機体の `.repos` をビルド

---

## 8. 次にやること

| 順 | 作業 | 担当 |
|---|---|---|
| 1 | 組織に `gamma`（管理）と `handbook` を作る | 人 |
| 2 | `gamma` README を 4 章の雛形 + gamma_main の該当節で作成 | Claude |
| 3 | gamma_main README を分割（3 章） | Claude |
| 4 | handbook の 4 ファイル初稿 | Claude |
| 5 | attracts_ws に `beta.repos` / `gamma.repos` を追加、README の URL 修正 | 人 / Claude |
| 6 | gamma_main README の未決項目（2 章）を順に埋める | 相談しながら |

### 参照したリポジトリ

- rabbits-robotics/beta_main: 射出 ESC の実装値、rabcl の submodule 運用、ビルド手順
- rabbits-robotics/rabcl: 既存コンポーネント（UART / CAN / OmniDrive / PdGravityFf / BNO055 / JGA25_370 / LD_20MG）
- rabbits-robotics/attracts_ws: `.repos` と CI の構成
