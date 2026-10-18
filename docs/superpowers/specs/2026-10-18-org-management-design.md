# Rabbits 組織のリポジトリ管理方針 設計ドキュメント

- 日付: 2026-10-18
- 対象: rabbits-robotics 組織全体（alpha / beta / gamma とその周辺リポジトリ）
- 置き場: `rabbits-robotics/.github` の `docs/superpowers/specs/`（gamma_main での仮置きから 2026-10-18 に移動）
- 前段の検討: [2026-10-18-repo-management-notes.md](./2026-10-18-repo-management-notes.md)（2026-10 の検討メモ）

---

## 1. 目的と背景

機体ごとの情報（BOM、構成、基板データ、配線、設計判断）が、スプレッドシート・ローカルの KiCad・Discord の投稿に散らばっていて、後から探せない。
これを GitHub 上に集約し、「機体名を知っていれば、そこから全部たどれる」状態を作る。

最上位ルール（組織としてのリポジトリの切り方・命名・ライセンス・各種別の運用）を先に固め、各機体のリポジトリはそれに従う。

### 使う人

- 現在は 1 人（kota0314）。全員 git / GitHub を使う前提でよい
- 将来メンバーが増える可能性はあるが、今の段階では「git を触れない人向けの配慮」はしない

### 公開方針

- 個人情報などプライバシーに関わるもの以外はすべて公開する
- 既存の 17 リポジトリはすでにすべて public。ただし LICENSE ファイルはどこにも無く、組織プロフィールも空

---

## 2. ゴール・非ゴール

### ゴール

- 組織トップページ（https://github.com/rabbits-robotics）から運用ルールと各機体の入口にたどれる
- 機体ごとに管理リポジトリがあり、BOM・構成・設計判断・配線・写真・関連リポジトリへのリンクが集まっている
- リポジトリの種類ごとに命名・ライセンス・最低限の運用が決まっていて、新しい機体や基板を作るときに迷わない
- 1 年後に gamma の情報を探すとき、Discord を掘り返さずに済む

### 非ゴール（今回はやらない）

- メカ CAD のネイティブファイルを git で履歴管理すること
- ブランチ保護・PR 必須などのチーム運用ルール（メンバーがほぼいないため後回し）
- ドキュメントの英語化（当面日本語。方針としても明記しない）
- 管理リポジトリの細かい項目の確定（運用しながら増やす）
- Jetson 向けの環境構築手順（Jetson 構成に未着手）

### やってはいけないこと

- 基板リポジトリから機体管理リポジトリへの逆参照（相互参照を避ける。参照は機体 → 基板の一方向のみ）
- GitHub Releases に一度添付した CAD zip の差し替え（直すときは新しいリリースを切る）
- メカ CAD のネイティブファイルを LFS 無しで git に入れること

---

## 3. 制約

- GitHub には組織そのものに文書を置く機能が無い。組織トップに表示できるのは `.github` リポジトリの `profile/README.md` のみ
- GitHub Releases の添付は 1 ファイル 2GiB まで。リリース全体の容量・転送量に公式の上限は無い
- Git LFS の無料枠は保存 10GB・転送 10GB/月。mech_ws は現状 3.9GB
- KiCad 8（S 式テキスト形式）。回路図・基板ファイル自体は各数百 KB で、LFS は不要
- SolidWorks はアセンブリが部品を相対パスで参照する。フォルダ構成を崩さずに保存する必要がある
- rabcl はファーム（git submodule）と ROS 2（vcstool `.repos`）の 2 経路から参照される

---

## 4. 決定事項

### 4.1 最上位ルールの置き場

- 組織リポジトリ `rabbits-robotics/.github` を作り、これを handbook とする
- `profile/README.md` が組織トップに表示される。ここに Rabbits の紹介、機体一覧へのリンク、ルールの目次を置く
- 全リポジトリに共通するルールを `docs/repositories.md`（リポジトリ運用方法）にまとめ、種類ごとの運用ルールはそれを前提とした種類別の Markdown に分けて置く。profile README からリンクする
- ライセンスは種類別ファイルに、選定理由とあわせて書く（独立した `license.md` は作らない）
- 検討メモ 7 章の「`handbook` リポジトリ + `.github` に 1 行リンク」案は取り下げる

構成:

```
.github/
├── profile/README.md        組織トップ。紹介、機体一覧、ルール目次
├── LICENSE                  CC BY 4.0
└── docs/
    ├── repositories.md      リポジトリ運用方法。全リポジトリ共通の上位ドキュメント。
    │                        種類・命名、README の章立て・ステータス記号・設計判断の残し方、
    │                        新しい機体を作るときの手順
    ├── robot-repo.md        機体管理リポジトリの中身
    ├── firmware-repo.md     ファームウェアリポジトリの運用
    ├── pcb-repo.md          基板リポジトリの運用
    ├── common-lib.md        共通ライブラリ（rabcl など）の運用
    └── ros2-workspace.md    attracts_ws の運用
```

後回しにした事項（ブランチ運用、メカ CAD の普段の置き場など）は該当ファイルの末尾に「未決」として書く。

### 4.2 リポジトリの種類・命名・ライセンス

| 種類 | 命名 | 既存・予定 | ライセンス |
|---|---|---|---|
| 機体管理 | `機体名` | `alpha` / `beta` / `gamma`（新設） | CC BY 4.0 |
| ファームウェア | `基板名_fw`（市販ボード向けは `製品名_役割_fw`）。既存リポジトリは rename しない | alpha: `proto2_turret` / `proto2_yaw` / `proto2_main` / `proto2_chassis`、beta: `beta_main`、gamma: `gamma_main`、ほか `m5_basic_2_7_attracts`（いずれも既存名を維持） | MIT |
| 基板 | `基板名_pcb`、1 基板 1 リポジトリ | `gamma_turret_pcb` / `gamma_neck_pcb` / `gamma_chassis_pcb` / `beta_main_pcb` / `beta_chassis_pcb` / `proto2_turret_pcb` / `proto2_yaw_pcb` / `proto2_chassis_pcb` / `fuse_box_pcb` / `rsg_devboard_pcb`（新設、board_ws から） | CERN-OHL-P-2.0 |
| 共通ライブラリ | 増えたときの命名規則は未決 | `rabcl` | MIT |
| ROS 2 | `rabbits_役割`。既存ライブラリの ROS 2 ラッパーは `ライブラリ名_ros2`。既存の `attracts_*` は `rabbits_*` に rename する | `rabbits_ws` / `rabbits_bridge` / `rabbits_description` / `rabbits_interface` / `rabbits_msgs`（現 `attracts_*`）、`rabcl_ros2` | MIT |
| 組織ルール | `.github` | 新設 | CC BY 4.0 |

- 管理リポジトリ名に `_attracts` などの接尾辞は付けない（一度検討して取り下げ）
- `rabcl_ros2` は共通ライブラリではなく ROS 2 パッケージ（rabcl のラッパー）として扱う
- ROS 2 リポジトリは競技に依存しない名前にする。ATTRACTS 固有の要素はパッケージ内部の名前（`AttractsCommand.msg` など）で表す
- `test_f303k8_*`（alpha 初版用のテスト実装）と `can_test` は種類として扱わず、対象外とする。アーカイブするかは未決
- 基板リポジトリは機体をまたいで使い回す前提。機体管理リポジトリ側から「この機体はこの基板のこのタグを使う」とリンクする

ライセンスの選び方の根拠（handbook では種類別ファイルにそれぞれ書く）:

- 「クレジットを残してくれれば、それ以外は自由」が方針
- ソフトウェアは MIT。最も短く、著作権表示の保持のみを要求する
- ハードウェアは CERN-OHL-P-2.0。MIT は「Software」の複製を対象にしており、設計データから製造した基板そのものには効きにくい。CERN-OHL-P は製品や付属文書への出典表示を明記している。CC ライセンスはハードには非推奨（Creative Commons 自身が表明）
- ドキュメントは CC BY 4.0。文章・図・写真向けで、クレジットの書き方まで規定している
- 既存の全リポジトリにも種別に応じた LICENSE ファイルを追加する

### 4.3 機体管理リポジトリ（`alpha` / `beta` / `gamma`）

機体の入口。「ここを見れば全部わかる」場所。

入れるもの（確定分）:

- 関連リポジトリへのリンク（ファーム、基板とそのタグ、共通ライブラリ、ROS 2、attracts_description）
- BOM。Markdown 表。用途は見返しと将来の公開記録で、発注管理ではない。型番・数量・購入先リンクに加え「なぜその部品を選んだか」の一言を残す。作成するのは正式な公開時や発注時で、過去の記録（スプレッドシート）は上げ直さない。価格・予備込みの発注数を載せるかは未決
- 構成（システム構成図、基板間接続、電源系統、アクチュエータ一覧）
- 設計判断の記録（4.8 参照）
- 配線図
- 写真
- メカ CAD の節目ごとの zip（GitHub Releases に添付。4.7 参照）

細かい項目は運用しながら増やす。現時点で確定させない。

README の雛形は検討メモ 4 章のものを下敷きにする。alpha は最新世代（proto2）を現行として書き、proto1 からの経緯は書かない。gamma_main README のうち機体全体に関わる節（システム構成・データフロー、基板間接続、電源系統、gamma_neck / gamma_chassis、beta からの変更点）は、`gamma` リポジトリができてから移す（検討メモ 3 章）。

### 4.4 ファームウェアリポジトリ

- 共通ライブラリ（rabcl など）を git submodule で参照する（beta_main の現行どおり）
- ビルドシステムと開発環境は基板ごとに選び、README に手順を書く（PlatformIO に限定しない）
- README の章立ては beta_main の README（システム構成 → 基板詳細 → 前世代からの変更点 → 共通仕様 → 依存ライブラリ → ビルド方法）を下敷きにする
- 各項目にステータス記号を付ける（✅ 確定・実装済み / 🔧 実装中 / 💭 構想段階）

### 4.5 基板リポジトリ

- 1 基板 1 リポジトリ。1 枚だけ改版することも、機体をまたいで使い回すこともあるため
- `gamma_turret` は board_ws 上にローカルの git 履歴がある（2026-08-10 からの 10 コミット、remote 無し、どこにも push されていない）。これに remote を足し、履歴ごと `gamma_turret_pcb` として push する。検証用スクリプト（`check_nets.py` / `expected_nets.csv` / `erc.rpt`）も同居してよい（ユーザー未確認。基板リポジトリを作る段階で確認する）
- ただしこの履歴には除外対象（`fp-info-cache`、autosave、展開済みガーバー 4 世代）と、他基板のバックアップ zip 46 個（`beta_main-backups` / `beta_turret-backups` / `proto2_turret-backups`）がコミット済み。push 前に `git filter-repo` で他基板のバックアップとキャッシュだけ履歴から消すか（推奨）、そのまま push するかは未決。`old_design/`（旧 STM32 版）は gamma の経緯なので残す
- 他の基板は履歴が無いので新規作成する。各基板の現在の KiCad データは「最後に発注した版」と一致しているので、初回コミットにタグを打ち、改版はそこから起こす。README が埋まる前でも TODO 付きで push してよい
- `proto2_chassis_b` / `proto2_turret_b` / `proto2_yaw_b` は同じ基板の Rev B として、`proto2_*_pcb` に後続コミット + タグで積む
- board_ws の `chassis/` `turret/` `main/` `test_a` `test_b` は対象外（`chassis/` `turret/` `main/` は proto2 の古い残骸と推測。要確認）

README に必ず書く項目（2 つ。改版履歴はタグとリリースで管理し、組立・接続時の注意は書かない）:

| 項目 | 内容 |
|---|---|
| 基板の役割 | 何をする基板か 1〜2 行 |
| 主要部品 | マイコン、CAN トランシーバ、レギュレータなど、仕様を決めている部品 |

- 使用機体は書かない（相互参照を避ける）
- BOM は KiCad から CSV で書き出し `bom.csv` として置く。README の表にはしない

git に入れるもの・除外するもの:

| 扱い | 対象 | 理由 |
|---|---|---|
| 入れる | `*.kicad_pro` / `*.kicad_sch` / `*.kicad_pcb` | 設計そのもの |
| 入れる | `sym-lib-table` / `fp-lib-table` / `*.kicad_sym` / `*.pretty/` | プロジェクト固有ライブラリ。無いと開けない |
| 入れる | 回路図 PDF、`*.step`、`*.DXF` | KiCad 無しで参照できる |
| Releases に添付 | 発注向けガーバー zip（`gbr_YYYYMMDD.zip`） | 「この基板はこのデータで作った」の証拠。発注ごとにタグ + リリースを作り添付する。git には入れない |
| 除外 | `*-backups/` / `*.kicad_sch-bak` / `_autosave-*` / `#auto_saved_files#` | KiCad の自動バックアップ |
| 除外 | `fp-info-cache` / `*.kicad_prl` | キャッシュと個人のローカル表示設定 |
| 除外 | `*.dsn` / `*.ses` / `*.rules` / `*.net` | Freerouting の中間ファイル、古いネットリスト。再生成できる |
| 除外 | ガーバー zip と展開済みフォルダ（`gbr_*`） | Releases に添付するため git には入れない |

### 4.6 共通ライブラリ（rabcl など）

- rabcl は独立した種類として扱う。今後、画像認識などで共通ライブラリが増える見込みがあるため
- ファームからは git submodule、ROS 2 からは attracts_ws の `.repos` で参照する
- 参照側（submodule と `.repos`）はコミットを固定し、同じ機体では両方が同じコミットを指すようにする。タグを打つかどうかは参照側の都合で決める
- 何を rabcl に入れて何を機体側に残すかの線引き、ライブラリが増えたときの命名規則は `common-lib.md` に追記していく（命名規則は未決）

### 4.7 ROS 2 ワークスペース（attracts_ws）

- attracts_ws は 1 つのまま維持する
- 機体ごとに `beta.repos` / `gamma.repos` を分ける（現状は全パッケージ `version: main`）
- `.repos` は `vcs export --exact src` でコミット固定し、機体のマイルストーンごとに更新する
- `beta.repos` は、ユーザーが beta 実機に入っているコードを push してから（2026-10-21 水曜の予定）作る。それまで着手しない
- `alpha.repos` は今後検討（alpha で動いていたコミットが不明）
- 機体固有の launch / パラメータ / URDF は attracts_description に集約する。機体管理リポジトリには置かない
- README の clone URL（`robot-trainee/attracts_ws`）を組織のものに直す
- Jetson 向け環境構築手順の追記は後回し（Jetson 構成に未着手）

### 4.8 メカ CAD

- 確定: 節目ごとに、機体管理リポジトリの GitHub Releases に CAD 一式（SolidWorks ネイティブ）を zip で添付する。一度添付した zip は差し替えず、直すときは新しいリリースを切る
- 公開するのは機体ごとに最新のものだけ（alpha = proto2、beta = beta_proto1、gamma = gamma_proto1）。旧世代や発注単位のフォルダ、`sample robot` / `to_isono` などは公開しない
- zip の作成はユーザーが行う。Claude は Releases への添付とリリースノートを担当する
- CAD zip のライセンスは CERN-OHL-P-2.0。リポジトリ本体（CC BY 4.0）と異なるので、リリースノートに明記する
- 容量: 現状の世代別フォルダは 61MB〜840MB で、zip 後はすべて 2GiB に収まる
- 未決: ネイティブ CAD の普段の置き場（クラウドストレージなど）、STEP・図面 PDF・BOM をリポジトリ本体に置くか
- Git LFS は「人が増えて同時編集が始まったら」の選択肢として handbook に書き残す

### 4.9 設計判断の記録

- 設計判断は、その対象を管理するリポジトリの `docs/` 以下に、日付付きで 1 件ずつ残す
- 機体全体に関わる判断は機体管理リポジトリ、基板に閉じた判断は基板リポジトリ、ファームに閉じた判断はファームリポジトリ
- 現在の結論は README に書き、経緯は `docs/` に残す
- handbook には「`docs/` 以下に日付付きで残すことを推奨する」とだけ書く。具体的なフォルダ名やファイル形式は書かない（使うツールは変わるため）
- 現時点では superpowers の spec（`docs/superpowers/specs/YYYY-MM-DD-題名-design.md`）がその形になっている。小さな判断なら「決めたこと」と「理由」だけの短いファイルでよい

### 4.10 公開範囲と言語

- 個人情報などプライバシー以外はすべて公開する
- 言語は当面日本語。将来英語化する可能性はあるが、方針として明記しない

---

## 5. 既存のものへの適用

| 対象 | やること |
|---|---|
| 既存 17 リポジトリ | 種別に応じた LICENSE ファイルを追加 |
| 組織プロフィール | `.github/profile/README.md` で埋める |
| board_ws の KiCad 18 プロジェクト | 機体に属する 8 基板（proto2 の `_b` は Rev B として同じリポジトリに積む）+ fuse_box + DevBoard を `*_pcb` リポジトリ化。`chassis/` `turret/` `main/` `test_a` `test_b` は対象外（要確認） |
| board_ws 直下の drawio（`network.drawio` / `wiring.drawio` など機体名無し） | alpha のもの。`alpha` リポジトリの配線図として置く |
| board_ws 直下の `発注リスト_gamma両基板.md`（単価入り） | 価格を公開 BOM に載せるかは未決 |
| mech_ws | 機体ごとに最新世代だけ（alpha_proto2 / beta_proto1 / gamma_proto1）。ユーザーが zip を作り、各機体管理リポジトリの Releases に添付 |
| Rabbits/docs/architecture.md、attracts_ws_architecture.md | 機体をまたぐ知見。置き場は未決（handbook か機体管理リポジトリの docs/） |
| mech_ws/planetary_gear_design.md、計算スクリプト | 同上 |
| 過去機体のスプレッドシート BOM | 上げ直さない。BOM は今後の正式な公開時・発注時に作る |
| attracts_ws README | clone URL を組織に修正 |
| beta_main の `.gitmodules` | rabcl の URL が旧組織（`robot-trainee`）のまま。submodule のコミットを固定する際に組織 URL へ直す |
| gamma_main README | `gamma` リポジトリができてから、機体全体の節を移して分割（検討メモ 3 章） |
| ベンダー配布物（LK モータツール、dm-tools、ATTRACTS_SimpleClient） | リポジトリには入れず、入手先リンクとバージョンだけ書く |

---

## 6. 未決・後回し

| 項目 | 状態 |
|---|---|
| ブランチ保護・PR 必須の運用 | メンバーがほぼいないため後回し |
| メカ CAD のネイティブの普段の置き場 | 未決 |
| STEP・図面 PDF・BOM をリポジトリ本体に置くか | 未決 |
| 機体管理リポジトリの細かい項目 | 運用しながら増やす |
| 機体に属さない知見（architecture.md、遊星ギア資料）の置き場 | 未決 |
| 共通ライブラリが増えたときの命名規則 | 未決 |
| `test_f303k8_*` / `can_test` をアーカイブするか | 未決 |
| gamma_turret の履歴を `git filter-repo` で整理してから push するか | 未決（基板リポジトリを作る段階で決める） |
| BOM に価格・予備込み発注数を載せるか | 未決 |
| `alpha.repos` | alpha で動いていたコミットが不明。今後検討 |
| Jetson 向け環境構築手順 | Jetson 構成に着手してから |
| gamma_main README の未決項目（通信プロトコル、CAN ID、脚の制御モードなど） | 別途相談しながら埋める（検討メモ 2 章） |

---

## 7. 進め方

ユーザーは「実装前に方針を確認し、決めるべきことは一緒に決める」ことを求めている。各段階の文面はユーザーがレビューしてから push する。

| 順 | 作業 | 備考 |
|---|---|---|
| 1 | `rabbits-robotics/.github`（作成済み、空）を clone し、profile README と `docs/` の 6 ファイルと LICENSE を書いて初回コミット・push | 文面は本ドキュメントの 4 章を展開する。ファイルごとにレビューを経てコミット |
| 2 | 既存 17 リポジトリに LICENSE を追加 | 種別ごとに MIT / CERN-OHL-P-2.0 / CC BY 4.0 |
| 3 | `alpha` / `beta` / `gamma` を作成し、README を書く | gamma は検討メモ 4 章の雛形 + gamma_main の該当節。alpha は proto2 を現行として書く。BOM は作らない |
| 4 | board_ws から `*_pcb` リポジトリを作成 | gamma_turret は履歴の整理方針を決めてから。他は `.gitignore` 適用後に新規作成し、初回コミットにタグ |
| 5 | ユーザーが作った最新世代の CAD zip を各機体の Releases に添付 | リリースノートに CERN-OHL-P-2.0 を明記 |
| 6 | `attracts_*` を `rabbits_*` に rename（リポジトリ名、package.xml、CMake、依存指定、`.repos`、CI）し、rabbits_ws に `beta.repos` / `gamma.repos` を追加、README の URL を修正 | `beta.repos` は 2026-10-21 の push 後 |
| 7 | gamma_main README を分割し、検討メモと本ドキュメントを `.github/docs/` へ移す | |

---

## 8. 実装者に任せる範囲

- handbook 各ファイルの文面と見出しの細部（内容は 4 章のとおり。文面はユーザーレビューを経る）
- LICENSE ファイルの正確なテキスト（各ライセンスの公式テキストをそのまま使う。著作権表示は `Rabbits Robotics`）
- `.gitignore` の正確な記法（除外対象は 4.5 の表のとおり）
- 基板リポジトリの README テンプレートのレイアウト（必須 4 項目は 4.5 のとおり）
