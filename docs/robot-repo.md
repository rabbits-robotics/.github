# 機体管理リポジトリ

機体の入口となるリポジトリです。機体名をそのままリポジトリ名にします（例: `gamma`）。
機体全体の構成と、関連リポジトリへのリンクをここに集めます。

## 置くもの

| 内容 | 置き場 |
|---|---|
| 関連リポジトリへのリンク、システム構成、基板構成、アクチュエータ一覧、共通仕様 | README |
| BOM | `bom.md` |
| システム構成図、配線図 | `diagrams/`（元ファイルと PNG。無ければ README に ASCII 図） |
| 写真 | `images/`（代表 1 枚を README 冒頭に） |
| 設計判断の記録 | `docs/` |
| メカ CAD 一式 | GitHub Releases（後述） |

関連リポジトリの参照は機体管理リポジトリからの一方向です。基板やファームウェアの README に、使用機体は書きません。
製作中の機体は項目ごとにステータス記号（✅ 確定・実装済み / 🔧 実装中 / 💭 構想段階）を付け、完成した機体には付けません。

## README テンプレート

````markdown
# 機体名

機体の 1〜2 行の説明。

![機体名](images/機体名.jpg)

## 関連リポジトリ

| 役割 | リポジトリ | 備考 |
|---|---|---|
| 制御基板 | [xxx_pcb](https://github.com/rabbits-robotics/xxx_pcb) | v1.0（YYYY-MM-DD 発注） |
| 制御基板ファームウェア | [xxx_fw](https://github.com/rabbits-robotics/xxx_fw) | |
| 共通ライブラリ | [rabcl](https://github.com/rabbits-robotics/rabcl) | コミット固定 |
| ROS 2 ワークスペース | [rabbits_ws](https://github.com/rabbits-robotics/rabbits_ws) | `機体名.repos` |
| 機体固有設定（launch / パラメータ / URDF） | [rabbits_description](https://github.com/rabbits-robotics/rabbits_description) | |
| メカ CAD | 本リポジトリの Releases | CERN-OHL-P-2.0 |

## 環境構築（上位コントローラ側）

rabbits_ws を clone して `vcs import src < 機体名.repos` → `colcon build`。

## システム構成

上位コントローラからモータまでの経路（ASCII 図か diagrams/ の PNG）。

## 基板構成

基板と役割の表。必要なら基板間接続、電源系統、組立時の注意を小節で足す。

## アクチュエータ一覧

| 部位 | モータ | バス |
|---|---|---|

## 共通仕様

上位コントローラ、CAN ビットレート、制御周期。

## 前世代からの変更点（前世代があれば）

## 写真

images/ の残りをリンクで並べる。

## 設計メモ

機体全体に関わる設計判断は `docs/` に日付付きで残す。

## ライセンス

このリポジトリの文書は © Rabbits Robotics、[CC BY 4.0](./LICENSE)。
Releases に添付するメカ CAD は CERN-OHL-P-2.0（リリースノートに明記）。
````

## BOM

`bom.md` に Markdown の表で書きます。見返しと公開記録が目的で、発注管理には使いません。正式な公開時や発注時に作ります。

````markdown
# 機体名 BOM

| 部位 | 部品 | 型番 | 数量 | 購入先 | 選定理由 |
|---|---|---|---|---|---|
| 車輪 | モータ | DM2325 | 4 | [DAMIAO](https://...) | |
````

## メカ CAD

節目ごとに、CAD 一式（SolidWorks ネイティブ）を zip にして GitHub Releases に添付します。

- 1 ファイル 2GiB までです
- 一度添付した zip は差し替えません。直すときは新しいリリースを切ります
- 公開するのは機体ごとに最新の世代だけです
- zip のライセンスは CERN-OHL-P-2.0 です。リポジトリ本体（CC BY 4.0）と異なるので、リリースノートに明記します

## ライセンス

CC BY 4.0 です。文章、図、写真向けのライセンスで、クレジットの書き方まで規定しています。
LICENSE ファイルには CC BY 4.0 の公式テキストをそのまま置き、著作権表示は `Rabbits Robotics` にします。
