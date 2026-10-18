# Rabbits Robotics

[ATTRACTS](https://attracts-game.com/) 向けの対戦ロボット等を開発しているチームです。
機体ごとの設計データ、ファームウェア、ROS 2 パッケージをこの organization で公開しています。

## チーム方針

- 個人情報などプライバシーに関わるもの以外は、すべて公開します

## Robots

機体名のリンク先が各機体の管理リポジトリで、ここが入口です。構成、基板、配線、関連リポジトリへのリンクはそこから辿れます。

### ATTRACTS

[ATTRACTS](https://attracts-game.com/) 向けの対戦ロボットです。

| 機体 | 概要 |
|---|---|
| [alpha](https://github.com/rabbits-robotics/alpha) | 四輪オムニ、無限回転できるヨー軸とピッチ軸を搭載。コストを抑えた廉価版のテスト機体 |
| [beta](https://github.com/rabbits-robotics/beta) | alpha と同じ構成でスペックを向上させたフラグシップ機体 |
| [gamma](https://github.com/rabbits-robotics/gamma) | beta をベースに、車高調整できる脚機構と首のロール軸を追加した最新機体。開発中 |

## リポジトリ運用ルール

全リポジトリに共通するルールを [リポジトリ運用方法](https://github.com/rabbits-robotics/.github/blob/main/docs/repositories.md) にまとめています。
新しい機体や基板を作るときは、まずこれを読んでください。
リポジトリの種類ごとのルールは、リポジトリ運用方法を前提として、種類別のドキュメントに書いています。

- [リポジトリ運用方法](https://github.com/rabbits-robotics/.github/blob/main/docs/repositories.md): リポジトリの種類と命名、README の書き方、新しい機体を作るときの手順
  - [機体管理リポジトリ](https://github.com/rabbits-robotics/.github/blob/main/docs/robot-repo.md): 機体の入口となるリポジトリに置くもの
  - [ファームウェアリポジトリ](https://github.com/rabbits-robotics/.github/blob/main/docs/firmware-repo.md): ファームウェアの構成とビルド
  - [基板リポジトリ](https://github.com/rabbits-robotics/.github/blob/main/docs/pcb-repo.md): KiCad データの扱いと README の必須項目
  - [共通ライブラリ](https://github.com/rabbits-robotics/.github/blob/main/docs/common-lib.md): rabcl などの参照方法とタグ運用
  - [ROS 2 ワークスペース](https://github.com/rabbits-robotics/.github/blob/main/docs/ros2-workspace.md): rabbits_ws と機体ごとの `.repos`

各種類のライセンスと選定理由は、種類別のドキュメントに書いています。

## ライセンス

このリポジトリの文書は © Rabbits Robotics、[CC BY 4.0](https://github.com/rabbits-robotics/.github/blob/main/LICENSE) です。
各リポジトリのライセンスは、それぞれの LICENSE ファイルを参照してください。
