# ウェブカートグラフィ入門

立命館大学 2026年度 秋セメスターの授業「オープンデータで始めるウェブカートグラフィ入門」のスライドです。

スライドは [Marp](https://marp.app/) で書いていて、main に push すると GitHub Pages に公開されます。

https://kamataryo.github.io/rits-2026-fall-cartography/

授業の目標やスケジュールは [syllabus.md](./syllabus.md) を見てください。

## スライドのビルド

Node.js 24 以上と pnpm が必要です。

```shell
pnpm install
pnpm dev        # http://localhost:8080 でプレビュー（変更を監視）
pnpm build      # HTML を output/ に出力
pnpm build:all  # HTML・PDF・PPTX をまとめて出力
```

エディタでのプレビューには [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) が便利です。

## ディレクトリ

- `slides/` — 各回のスライド。ファイル名の先頭の番号が回数です。`_` で始まるものは下書きで、ビルドには含まれますが授業ではまだ使っていません。
- `slides/images/` — スライドで使う画像。ビルド時に `output/images/` にコピーされます。
- `vector_tile.proto`, `vector-tile.sample.*` — ベクトルタイルの回で使うサンプル

## ライセンス

MIT
