# リポジトリ運用方法

rabbits-robotics organization の全リポジトリに共通するルールです。
リポジトリの種類ごとのルールは、このドキュメントを前提として種類別のドキュメントに書いています。

## 基本方針

- 機体管理リポジトリから必要な情報にアクセスできるようにします。機体ごとに管理リポジトリを置き、ファームウェア、基板、共通ライブラリ、ROS 2 パッケージへのリンクをそこに集めます
- 参照は機体管理リポジトリから各リポジトリへの一方向です。各リポジトリから機体管理リポジトリへは逆参照しません。機体管理リポジトリ以外は、機体をまたいで使うことがあるためです

## リポジトリの種類と命名

| 種類 | 命名 | 例 | ライセンス | 詳細 |
|---|---|---|---|---|
| 機体管理 | `機体名` | `alpha` / `beta` / `gamma` | CC BY 4.0 | [robot-repo.md](./robot-repo.md) |
| ファームウェア | `基板名_fw`。市販ボード向けは `製品名_役割_fw` | `gamma_turret_fw` / `m5_basic_controller_fw` | MIT | [firmware-repo.md](./firmware-repo.md) |
| 基板 | `基板名_pcb`。1 基板 1 リポジトリ | `gamma_turret_pcb` / `fuse_box_pcb` | CERN-OHL-P-2.0 | [pcb-repo.md](./pcb-repo.md) |
| 共通ライブラリ | - | `rabcl` | MIT | [common-lib.md](./common-lib.md) |
| ROS 2 | `rabbits_役割`。既存ライブラリの ROS 2 ラッパーは `ライブラリ名_ros2` | `rabbits_ws` / `rabbits_msgs` / `rabcl_ros2` | MIT | [ros2-workspace.md](./ros2-workspace.md) |
| organization ルール | `.github` | このリポジトリ | CC BY 4.0 | |

### ライセンス

方針は「クレジットを残してくれれば、それ以外は自由」です。
種類ごとに採用するライセンスと、その理由、LICENSE ファイルの付け方は種類別のドキュメントに書いています。
LICENSE ファイルの著作権表示は、個人名ではなく organization 名 `Rabbits Robotics` にします。

## README の書き方

各リポジトリの README には、現在の結論だけを書きます。そこに至った経緯は `docs/` 以下に残すことを推奨します。リポジトリの責任範囲外の判断は書きません。

README の章立ては種類ごとに決めています。種類別のドキュメントを参照してください。

## 新しい機体を作るときの手順

1. 機体名を決めます。これがそのまま管理リポジトリ名になります
2. 機体管理リポジトリ `機体名` を作り、README を書きます（[robot-repo.md](./robot-repo.md)）
3. 新しく設計した基板は `基板名_pcb` リポジトリを作ります。既存の基板を使い回す場合は作らず、使うタグを機体管理リポジトリからリンクします（[pcb-repo.md](./pcb-repo.md)）
4. ファームウェアリポジトリ `基板名_fw` を基板ごとに作ります（[firmware-repo.md](./firmware-repo.md)）
5. `rabbits_ws` に `機体名.repos` を追加し、機体固有の launch / パラメータ / URDF は `rabbits_description` に置きます（[ros2-workspace.md](./ros2-workspace.md)）
6. 各リポジトリに種類に応じた LICENSE ファイルを置きます
7. 機体管理リポジトリの README に関連リポジトリをリンクし、[organization トップの機体一覧](../profile/README.md)に機体を追加します
