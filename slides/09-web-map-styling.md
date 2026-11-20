---
marp: true
title: 'ウェブカートグラフィ入門 第9回: スタイルの基本（ポイント、ライン、ポリゴン）'
theme: default
class: title
paginate: true
---

<style>
.cols { display: flex; gap: 24px; align-items: center; }
.cols > div { flex: 1; min-width: 0; }
</style>

<!-- _class: title -->

# オープンデータで始める<br />ウェブカートグラフィ入門

## 第9回: スタイルの基本（ポイント、ライン、ポリゴン）<br />データの中身で描き分ける

立命館大学 2026年度 秋セメスター 火曜5限
授業時間: 95分

---

## 本日のアジェンダ

1. **前回の振り返り・課題の確認** (10分)
2. **paint と layout** (10分)
3. **実習1: 見た目を整える** (20分)
4. **データで描き分ける** (20分)
5. **実習2: properties で描き分ける** (25分)
6. **まとめ・課題** (10分)

---

<style scoped>section { font-size: 26px; }</style>

## 前回の振り返り

### 第8回の主要ポイント

- MapLibre GL JS は、**Source**（何を描くか）と **Layer**（どう描くか）で地図を作る
- Layer の `type` はジオメトリに合わせる（`circle` / `line` / `fill`）
- GeoJSON を読み込むページは、**ローカルサーバー**で開く
- **イベント**で、クリックなどの操作に反応する地図になる

### 課題の確認

前回作った地図ページを見せてください

- どのデータを重ねたか。ポップアップには何を出したか
- 地図を見て、**どれが何なのか**はすぐにわかる？

---

## 今日のゴール

<div class="cols">
<div>

前回のデータは、**全部同じ色・同じ大きさ**だった

今日は

1. 色・大きさ・太さを**整える**
2. `properties` の中身で**描き分ける**
3. ズームに合わせて**変える**

=> クリックしなくても、見ただけで伝わる地図にする

</div>
<svg width="500" height="250" viewBox="0 0 500 250" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="左は全ての点が同じ赤色・同じ大きさの地図。右は点が種類ごとに色分けされ、大きさも変えられた地図">
  <defs><marker id="goal-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#666"/></marker></defs>
  <text x="105" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">前回</text>
  <rect x="1" y="30" width="208" height="200" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <line x1="1" y1="150" x2="209" y2="120" stroke="#ccc" stroke-width="6"/>
  <line x1="80" y1="30" x2="100" y2="230" stroke="#ccc" stroke-width="6"/>
  <circle cx="45" cy="70" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="150" cy="65" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="125" cy="110" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="50" cy="180" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="165" cy="175" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="120" cy="200" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <line x1="222" y1="130" x2="275" y2="130" stroke="#666" stroke-width="2" marker-end="url(#goal-arrow)"/>
  <text x="395" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">今日</text>
  <rect x="291" y="30" width="208" height="200" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <line x1="291" y1="150" x2="499" y2="120" stroke="#ccc" stroke-width="6"/>
  <line x1="370" y1="30" x2="390" y2="230" stroke="#ccc" stroke-width="6"/>
  <circle cx="335" cy="70" r="6" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="440" cy="65" r="13" fill="#2f6fbf" stroke="#fff" stroke-width="2"/>
  <circle cx="415" cy="110" r="6" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="340" cy="180" r="9" fill="#3c8d3c" stroke="#fff" stroke-width="2"/>
  <circle cx="455" cy="175" r="6" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="410" cy="200" r="16" fill="#3c8d3c" stroke="#fff" stroke-width="2"/>
</svg>
</div>

---

## 何を、どう描き分けるか

見た目の違いには、それぞれ得意な役割がある

| 伝えたいこと | 向いている見た目 | 例 |
|--|--|--|
| **種類**の違い | 色合い（赤・青・緑）、形 | カフェは赤、図書館は青 |
| **量**の大小 | 大きさ、太さ、色の濃さ | 利用者が多い駅ほど大きい丸 |
| **重要さ** | 目立つ色、上に重ねる | おすすめの店だけ濃い色 |

### 考えてみよう

自分のデータで、見ただけで伝えたい違いは何だろう？

---

<style scoped>section { font-size: 22px; }</style>

## paint と layout

Layer の見た目は、`paint` と `layout` で決まる

```js
map.addLayer({
  id: 'my-points',
  type: 'circle',
  source: 'my-data',
  paint: {
    'circle-radius': 8,              // 半径（ピクセル）
    'circle-color': '#e5533d',       // 塗りの色
    'circle-opacity': 0.8,           // 透明度（0〜1）
    'circle-stroke-width': 2,        // ふちの太さ
    'circle-stroke-color': '#ffffff' // ふちの色
  },
  layout: { visibility: 'visible' }  // 表示する / しない
})
```

- **`paint`**: 色、大きさ、透明度など
- **`layout`**: 表示・非表示、文字の内容、線の端の形など
- どちらに何を書くかは覚えなくて OK。プロパティの名前は **`type` から始まる**

---

<style scoped>section { font-size: 24px; }</style>

## type ごとの主なプロパティ

| `type` | よく使うプロパティ |
|--|--|
| `circle` | `circle-radius` `circle-color` `circle-opacity` `circle-stroke-width` `circle-stroke-color` |
| `line` | `line-width` `line-color` `line-opacity` `line-dasharray`（破線） |
| `fill` | `fill-color` `fill-opacity` |
| `symbol` | `text-field`（文字の内容） `text-size` `text-color` `text-halo-color`（文字のふち） |

- 色は `'#e5533d'` や `'red'`、`'rgba(229, 83, 61, 0.5)'` のように書く
- 全部の一覧は [MapLibre Style Spec](https://maplibre.org/maplibre-style-spec/layers/) にある

---

## ポリゴンは Layer を重ねて描く

<div class="cols">
<div>

- `fill` は**塗り**だけ。輪郭線の太さは変えられない
- => 同じ Source から `line` の Layer をもう1つ作って、上に重ねる
- 名前を出したいときは、さらに `symbol` の Layer を重ねる

1つの Source から、複数の Layer を作れる（第8回）

</div>
<svg width="460" height="300" viewBox="0 0 460 300" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="下から fill の Layer、line の Layer、symbol の Layer が重なって、塗り・輪郭線・名前のついたポリゴンになる図">
  <polygon points="20,280 200,280 250,220 70,220" fill="#fff" stroke="#bbb" stroke-width="1.5"/>
  <polygon points="75,268 165,268 200,232 110,232" fill="#f7c9c0"/>
  <polygon points="20,185 200,185 250,125 70,125" fill="#fff" fill-opacity="0.7" stroke="#bbb" stroke-width="1.5"/>
  <polygon points="75,173 165,173 200,137 110,137" fill="none" stroke="#e5533d" stroke-width="3" stroke-linejoin="round"/>
  <polygon points="20,90 200,90 250,30 70,30" fill="#fff" fill-opacity="0.7" stroke="#bbb" stroke-width="1.5"/>
  <text x="137" y="66" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">公園</text>
  <text x="275" y="55" font-size="18" font-weight="bold" font-family="monospace" fill="#1d4f8f">symbol</text>
  <text x="275" y="78" font-size="14" fill="#333">名前</text>
  <text x="275" y="150" font-size="18" font-weight="bold" font-family="monospace" fill="#1d4f8f">line</text>
  <text x="275" y="173" font-size="14" fill="#333">輪郭線</text>
  <text x="275" y="245" font-size="18" font-weight="bold" font-family="monospace" fill="#1d4f8f">fill</text>
  <text x="275" y="268" font-size="14" fill="#333">塗り</text>
</svg>
</div>

---

## 実習1: 見た目を整える（20分）

### ゴール

前回の地図のデータを、ベースマップの上で**見やすく**する

### AI への頼み方の例

```text
data.geojson の見た目を変えてください。
- ポイントは半径 8 の丸にして、白いふちをつける
- ポリゴンは半透明に塗り、輪郭線を太さ 2 で描く
- ラインは太さ 4 の破線にする
```

頼んだあとは、`paint` の数字や色を**自分で書き換えて**確かめる

---

## 見やすくするコツ

- **ベースマップに埋もれない色**を選ぶ
  （淡色のベースマップなら濃い色、航空写真なら明るい色と白いふち）
- 重なりが多いときは、**半透明**にする
- 色の数は**絞る**。5色を超えると見分けにくい
- 色を選ぶときは [ColorBrewer](https://colorbrewer2.org/) が便利
  （地図のための色の組み合わせ集。色覚の違いに配慮した組み合わせも選べる）

---

<style scoped>section { font-size: 24px; }</style>

## データで描き分ける: Expression

色や大きさを**1つの値**ではなく、**データの中身から計算**させる書き方

<svg width="1000" height="190" viewBox="0 0 1000 190" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="properties の category が cafe なら赤、library なら青、それ以外は灰色の丸になる、という対応を示す図">
  <defs><marker id="ex-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#666"/></marker></defs>
  <text x="150" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#7a5d00">properties</text>
  <text x="500" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">Expression</text>
  <text x="850" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#1d4f8f">見た目</text>
  <rect x="1" y="32" width="298" height="42" rx="6" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="150" y="59" font-size="15" font-family="monospace" text-anchor="middle" fill="#333">"category": "cafe"</text>
  <rect x="1" y="88" width="298" height="42" rx="6" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="150" y="115" font-size="15" font-family="monospace" text-anchor="middle" fill="#333">"category": "library"</text>
  <rect x="1" y="144" width="298" height="42" rx="6" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="150" y="171" font-size="15" font-family="monospace" text-anchor="middle" fill="#333">"category": "park"</text>
  <rect x="371" y="32" width="258" height="154" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <text x="500" y="59" font-size="15" text-anchor="middle" fill="#333">cafe なら 赤</text>
  <text x="500" y="115" font-size="15" text-anchor="middle" fill="#333">library なら 青</text>
  <text x="500" y="171" font-size="15" text-anchor="middle" fill="#333">それ以外は 灰色</text>
  <line x1="302" y1="53" x2="366" y2="53" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <line x1="302" y1="109" x2="366" y2="109" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <line x1="302" y1="165" x2="366" y2="165" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <line x1="632" y1="53" x2="826" y2="53" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <line x1="632" y1="109" x2="826" y2="109" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <line x1="632" y1="165" x2="826" y2="165" stroke="#666" stroke-width="2" marker-end="url(#ex-arrow)"/>
  <circle cx="850" cy="53" r="12" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="850" cy="109" r="12" fill="#2f6fbf" stroke="#fff" stroke-width="2"/>
  <circle cx="850" cy="165" r="12" fill="#888888" stroke="#fff" stroke-width="2"/>
</svg>

```js
'circle-color': ['match', ['get', 'category'],
  'cafe', '#e5533d',
  'library', '#2f6fbf',
  '#888888'
]
```

---

## Expression の読み方

`[ ]` の**先頭が命令**、残りがその材料

```js
['get', 'category']   // properties の category を取り出す
['+', 1, 2]           // 1 + 2
```

材料の中に、さらに `[ ]` を入れられる

```js
['match', ['get', 'category'], 'cafe', '#e5533d', '#888888']
```

=> 「`category` を取り出して、`cafe` なら赤、それ以外は灰色」

内側の `[ ]` から順に読む

---

<style scoped>section { font-size: 24px; }</style>

## よく使う Expression

| 命令 | 使いどころ | 例 |
|--|--|--|
| `get` | `properties` の値を取り出す | `['get', 'name']` |
| `match` | **種類**ごとに値を決める | カテゴリーで色分け |
| `interpolate` | **数値**に合わせてなめらかに変える | 人数が多いほど大きい丸 |
| `zoom` | 今のズームレベルを取り出す | 拡大するほど太い線 |

```js
// visitors が 0 のとき半径 4、1000 のとき半径 20。その間はなめらかに変わる
'circle-radius': ['interpolate', ['linear'], ['get', 'visitors'], 0, 4, 1000, 20]
```

`interpolate` に使う `properties` は、`"100"`（文字列）ではなく `100`（数値）で書く（第5回）

---

<style scoped>section { font-size: 24px; }</style>

## ズームに合わせて変える

<div class="cols">
<div>

同じ大きさのままだと

- 引いたとき: 丸が重なって**つぶれる**
- 寄ったとき: 丸が**小さすぎる**

=> ズームレベルに合わせて大きさを変える

```js
'circle-radius': ['interpolate',
  ['linear'], ['zoom'],
  10, 3,
  16, 12
]
```

ズーム 10 で半径 3、ズーム 16 で半径 12

</div>
<svg width="460" height="320" viewBox="0 0 460 320" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="横軸がズームレベル、縦軸が丸の半径のグラフ。ズーム10で半径3、ズーム16で半径12になり、その間は直線的に大きくなる">
  <line x1="60" y1="270" x2="440" y2="270" stroke="#666" stroke-width="2"/>
  <line x1="60" y1="270" x2="60" y2="30" stroke="#666" stroke-width="2"/>
  <text x="250" y="312" font-size="15" text-anchor="middle" fill="#333">ズームレベル</text>
  <text x="20" y="150" font-size="15" text-anchor="middle" fill="#333" transform="rotate(-90 20 150)">半径</text>
  <polyline points="60,230 140,230 360,80 440,80" fill="none" stroke="#2f6fbf" stroke-width="3" stroke-linejoin="round"/>
  <line x1="140" y1="270" x2="140" y2="230" stroke="#999" stroke-width="1" stroke-dasharray="4 4"/>
  <line x1="360" y1="270" x2="360" y2="80" stroke="#999" stroke-width="1" stroke-dasharray="4 4"/>
  <line x1="60" y1="80" x2="360" y2="80" stroke="#999" stroke-width="1" stroke-dasharray="4 4"/>
  <text x="140" y="290" font-size="15" text-anchor="middle" fill="#333">10</text>
  <text x="360" y="290" font-size="15" text-anchor="middle" fill="#333">16</text>
  <text x="50" y="235" font-size="15" text-anchor="end" fill="#333">3</text>
  <text x="50" y="85" font-size="15" text-anchor="end" fill="#333">12</text>
  <circle cx="140" cy="200" r="4" fill="#e5533d" stroke="#fff" stroke-width="1.5"/>
  <circle cx="250" cy="120" r="10" fill="#e5533d" stroke="#fff" stroke-width="1.5"/>
  <circle cx="360" cy="48" r="16" fill="#e5533d" stroke="#fff" stroke-width="1.5"/>
</svg>
</div>

---

<style scoped>section { font-size: 26px; }</style>

## filter: 描く地物を絞る

Layer に `filter` を書くと、**条件に合う地物だけ**を描く

```js
// category が cafe の地物だけ
filter: ['==', ['get', 'category'], 'cafe']

// ポイントだけ
filter: ['==', ['geometry-type'], 'Point']
```

- 同じ Source から、条件の違う Layer をいくつも作れる
  （例: おすすめの店だけ、上に大きく重ねる）
- 前回の「ポリゴンの角に丸が出る」は、2つ目の `filter` で直せる

---

<style scoped>section { font-size: 26px; }</style>

## 文字を出す: symbol

`symbol` の Layer で、`properties` の値を**ラベル**として地図に出せる

```js
map.addLayer({
  id: 'my-labels',
  type: 'symbol',
  source: 'my-data',
  layout: {
    'text-field': ['get', 'name'],
    'text-size': 14,
    'text-offset': [0, 1.2],
  },
  paint: { 'text-halo-color': '#ffffff', 'text-halo-width': 2 },
})
```

- 文字が重なる場所では、MapLibre GL JS が**自動で間引く**
- 文字を描くには**フォント**のデータが要る。ラスタのベースマップでは足りないことがある

---

<style scoped>section { font-size: 26px; }</style>

## 実習2: properties で描き分ける（25分）

### ゴール

自分のデータを、`properties` の中身で描き分ける

### 準備

描き分けに使う項目（種類や数値）が `properties` になければ、[geojson.io](https://geojson.io/) で足す（第5回）

### AI への頼み方の例

```text
data.geojson のポイントを、properties の category の値で色分けしてください。
cafe は赤、library は青、それ以外は灰色にします。
ズームレベルが上がるほど、丸を大きくしてください。
```

---

## できたら試してみよう

1つずつ頼んで、動くのを確かめながら進める

- `properties` の `name` を**ラベル**で出す
- **凡例**を地図の隅に置く（どの色が何を表すか）
- 数値の `properties` で、丸の**大きさ**や色の**濃さ**を変える
- `filter` で、特定の種類だけを別の Layer にして目立たせる
- マウスを置いた地物だけ、色を変える

---

## 生成されたコードを読む

- `paint` の中で、値が `[ ]` になっているところを探す
- 先頭の命令は何？（`match` / `interpolate` / `get`）
- `match` の最後の値（**それ以外**の色）は何色？ 自分のデータでそれに当たる地物はある？
- `interpolate` の数字を1つ書き換えて、地図が変わるのを確かめる
- `filter` はある？ どんな条件？

---

<style scoped>section { font-size: 26px; }</style>

## うまくいかないとき

| 症状 | 確かめること |
|--|--|
| 全部が「それ以外」の色になる | `properties` の項目名や値のつづり。大文字・小文字、全角・半角 |
| 大きさが変わらない | 値が `"100"`（文字列）になっていないか |
| ラベルが出ない | Console のエラーを AI に貼る。フォントが足りないことが多い |
| 何も出なくなった | `[ ]` や `,` の対応が崩れていないか。Console のエラーを AI に貼る |
| 直すつもりが、動いていたところまで壊れた | 動いている状態のフォルダを、コピーして残しておく |

---

## 色分けしたら、凡例を

- 色や大きさの意味は、**作った人にしかわからない**
- 凡例（はんれい）は、地図の読み方を伝える **UI**（第7回）
- 地図の上に HTML で重ねる。AI に頼めば作ってくれる

```text
地図の左下に凡例を置いてください。
赤い丸は「カフェ」、青い丸は「図書館」、灰色の丸は「その他」です。
```

凡例の色と、`match` の色が**同じ**になっているかを確かめる

---

## まとめ

- Layer の見た目は **`paint`** と **`layout`** で決まる
- ポリゴンは `fill` + `line` + `symbol` のように、**Layer を重ねて**描く
- **Expression** で、`properties` やズームに合わせて見た目を変えられる
  （`get` / `match` / `interpolate` / `zoom`）
- **`filter`** で、Layer が描く地物を絞れる
- 種類は**色合い**、量は**大きさや濃さ**で描き分ける。色分けしたら**凡例**を置く

---

<div class="assignment">

## 課題: データを描き分けた地図を作る

### 課題内容

前回の地図のデータを、仮のテーマが伝わるように描き分けてください。

- `properties` の値によって、色か大きさが変わること
- ズームレベルによって、見た目が変わるところが1つ以上あること
- 凡例があること
- ベースマップと、重ねたデータの出典表記が表示されていること

</div>

---

<style scoped>section { font-size: 26px; }</style>

<div class="assignment">

### 提出要項

- **提出物**:
  - 作ったファイル一式（zip にまとめる。GitHub リポジトリの URL でも可）
  - 地図を表示した画面のスクリーンショット
  - メモ: 何を伝えるために、どう描き分けたか / AI に出した主な指示 / 生成されたコードの Expression について確認したこと・直したこと
- **提出期限**: 次回授業開始時
- **提出方法**: moodle+R 経由
- **評価ポイント**: 描き分けがテーマに合っているか、凡例、出典表記、生成されたコードを自分で確かめられているか

</div>

---

## 質疑応答

### 本日の内容について
- 授業内容に関する質問
- 課題に関する質問

### 連絡先
- moodle+R

---

<!-- _class: title -->

# ありがとうございました

## 次回もよろしくお願いします

**第10回: ベクトルタイルの活用**
