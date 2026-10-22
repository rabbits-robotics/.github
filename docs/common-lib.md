# 共通ライブラリ

ファームウェアと ROS 2 の両方から使うライブラリのリポジトリです（例: `rabcl`）。
機体をまたいで使うものだけを入れます。機体固有の設定値（CAN ID、ゲインなど）は機体側に置きます。

## 置くもの

| 内容 | 置き場 |
|---|---|
| ライブラリ本体 | `include/` / `src/` |
| テスト | `test/` |
| 設計判断の記録 | `docs/` |

## テスト

共通ライブラリ（rabcl、rabcl_ros2、rabcl_esp32 など）には必ずユニットテストを書き、CI で実行します。
複数の機体・ファームウェア・ROS 2 から使われるため、1 つの変更の影響範囲が大きいからです。
マイコンの API に依存する部分は `test/fakes/` に偽物を置いて差し替え、実機なしで論理を検証します（rabcl_esp32 が例）。実機でしか確認できないことは `examples/` に動作確認用のプロジェクトを置きます。

README の 2 行目（タイトルの直下）に CI のステータスバッジを置きます。

## 参照のされ方

| 参照元 | 参照方法 |
|---|---|
| ファームウェア | git submodule |
| ROS 2 | `rabbits_ws` の `機体名.repos` |

どちらもコミットを固定して参照します。タグを打つかどうかは参照側の都合で決めます。

## README テンプレート

````markdown
# ライブラリ名

ライブラリの 1〜2 行の説明。

## 構成

| ディレクトリ | 内容 |
|---|---|
| `component/` | モータ、センサのドライバ |
| `controller/` | 制御器 |
| `interface/` | 通信のフレーム定義 |
| `utils/` | |

## 使い方

### ファームウェアから

```bash
git submodule add https://github.com/rabbits-robotics/ライブラリ名.git lib/ライブラリ名
```

### ROS 2 から

`機体名.repos` に追加する。

## ビルド・テスト

```bash
colcon build --packages-select ライブラリ名
colcon test --packages-select ライブラリ名
```
````

## ライセンス

MIT です。最も短く、著作権表示の保持だけを求めるライセンスです。
LICENSE ファイルには MIT の公式テキストをそのまま置き、著作権表示は `Rabbits Robotics` にします。
