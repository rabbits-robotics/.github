# 機体管理リポジトリ

機体の入口となるリポジトリです。機体名をそのままリポジトリ名にします（例: `gamma`）。
機体全体の構成と、関連リポジトリへのリンクをここに集めます。

## 置くもの

| 内容 | 置き場 |
|---|---|
| 関連リポジトリへのリンク | README |
| システム構成、アクチュエータ一覧 | README |
| BOM | `bom.md` |
| システム構成図、配線図 | `diagrams/` |
| 写真 | `images/` |
| 設計判断の記録 | `docs/` |
| メカ CAD 一式 | GitHub Releases（後述） |

関連リポジトリの参照は機体管理リポジトリからの一方向です。基板やファームウェアの README に、使用機体は書きません。

システム構成図と配線図は `diagrams/` に元ファイルと PNG を置き、README には PNG を貼ります。描くツールは問いません。

## README テンプレート

````markdown
# 機体名

機体の 1〜2 行の説明。

## 関連リポジトリ

| 役割 | リポジトリ | 備考 |
|---|---|---|
| 制御基板 | [xxx_pcb](https://github.com/rabbits-robotics/xxx_pcb) | v1.0 |
| 制御基板ファームウェア | [xxx_fw](https://github.com/rabbits-robotics/xxx_fw) | |
| 共通ライブラリ | [rabcl](https://github.com/rabbits-robotics/rabcl) | v1.0 |
| ROS 2 ワークスペース | [rabbits_ws](https://github.com/rabbits-robotics/rabbits_ws) | `機体名.repos` |
| 機体固有設定（launch / パラメータ / URDF） | [rabbits_description](https://github.com/rabbits-robotics/rabbits_description) | |

## システム構成

![システム構成図](diagrams/system.png)

基板間の接続と電源系統を含めた全体図。配線図は [diagrams/wiring.png](diagrams/wiring.png)。

## 基板構成

| 基板 | 役割 |
|---|---|
| xxx | |

## アクチュエータ一覧

| 部位 | モータ | バス |
|---|---|---|
| | | |
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
