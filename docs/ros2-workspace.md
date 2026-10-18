# ROS 2 ワークスペース

ROS 2 パッケージは `rabbits_役割` の名前でリポジトリを分け（例: `rabbits_bridge`、`rabbits_msgs`）、`rabbits_ws` で 1 つのワークスペースに集めます。
`rabbits_ws` はパッケージの一覧（`.repos`）と環境構築の手順を持つだけで、コードは置きません。
各パッケージの役割と責務は `rabbits_ws` の README に一覧でまとめ、詳細は各パッケージの README に書きます。

## 機体ごとの `.repos`

- `rabbits_ws` に機体ごとの `機体名.repos` を置き、コミットを固定します
- 更新は機体のマイルストーンごとに行います
- 共通ライブラリは、その機体のファームウェアが submodule で参照しているものと同じコミットを指すようにします

## 機体固有の設定

launch、パラメータ、URDF は `rabbits_description` に集めます。機体管理リポジトリには置きません。

## ライセンス

MIT です。最も短く、著作権表示の保持だけを求めるライセンスです。
LICENSE ファイルには MIT の公式テキストをそのまま置き、著作権表示は `Rabbits Robotics` にします。
