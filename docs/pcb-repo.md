# 基板リポジトリ

基板 1 枚につき 1 リポジトリです。基板名に `_pcb` を付けた名前にします（例: `gamma_turret_pcb`）。
1 枚だけ改版することも、機体をまたいで使い回すこともあるため、機体単位ではまとめません。

## 置くもの

| 内容 | 置き場 |
|---|---|
| KiCad プロジェクト | ルート |
| 回路図 PDF、STEP、DXF | ルート |
| BOM | `bom.csv`（KiCad から書き出し） |
| 設計判断の記録 | `docs/` |
| 発注向けガーバー zip | GitHub Releases（後述） |

使用機体は書きません。機体管理リポジトリ側から、使う基板とそのタグをリンクします。

## git に入れるもの・除外するもの

| 扱い | 対象 | 理由 |
|---|---|---|
| 入れる | `*.kicad_pro` / `*.kicad_sch` / `*.kicad_pcb` | 設計そのもの |
| 入れる | `sym-lib-table` / `fp-lib-table` / `*.kicad_sym` / `*.pretty/` | プロジェクト固有ライブラリ。無いと開けない |
| 入れる | 回路図 PDF、`*.step`、`*.DXF` | KiCad 無しで参照できる |
| 除外 | `*-backups/` / `*.kicad_sch-bak` / `_autosave-*` / `#auto_saved_files#` | KiCad の自動バックアップ |
| 除外 | `fp-info-cache` / `*.kicad_prl` | キャッシュと個人のローカル表示設定 |
| 除外 | `*.dsn` / `*.ses` / `*.rules` / `*.net` | Freerouting の中間ファイル、古いネットリスト。再生成できる |
| 除外 | ガーバー（`gbr_YYYYMMDD.zip` と展開フォルダ） | Releases に添付する |

`.gitignore`:

```gitignore
# KiCad の自動バックアップ
*-backups/
*.kicad_sch-bak
_autosave-*
\#auto_saved_files\#

# キャッシュとローカル設定
fp-info-cache
*.kicad_prl

# Freerouting の中間ファイルと古いネットリスト
*.dsn
*.ses
*.rules
*.net

# ガーバー（Releases に添付する）
gbr_*
```

## 発注とリリース

発注するたびに、そのときのコミットにタグを打ち（例: `v1.0`、`v1.1`）、GitHub Releases を作ります。

- リリースノートに発注先と変更点を書きます
- 発注向けガーバー zip（`gbr_YYYYMMDD.zip`）を添付します
- 一度添付した zip は差し替えません。直すときは新しいリリースを切ります

改版の履歴はタグとリリースで管理し、README には書きません。
初回は現在の KiCad データが最後に発注した版と一致しているので、初回コミットにタグを打ちます。

## README テンプレート

````markdown
# 基板名_pcb

## 役割

何をする基板か、1〜2 行。

## 主要部品

| 部品 | 型番 | 用途 |
|---|---|---|
| マイコン | | |
| CAN トランシーバ | | |
| レギュレータ | | |
````

## ライセンス

CERN-OHL-P-2.0 です。
MIT は「Software」の複製を対象にしていて、設計データから製造した基板そのものには効きにくいです。CERN-OHL-P は製品や付属文書への出典表示を明記しています。CC ライセンスはハードウェア向けではないと Creative Commons 自身が表明しています。
LICENSE ファイルには CERN-OHL-P-2.0 の公式テキストをそのまま置き、著作権表示は `Rabbits Robotics` にします。
