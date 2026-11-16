---
marp: true
title: 'ウェブカートグラフィ入門 第8回: MapLibre GL JS の基礎操作'
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

## 第8回: MapLibre GL JS の基礎操作<br />自分のデータを重ねて、触れる地図にする

立命館大学 2026年度 秋セメスター 火曜5限
授業時間: 95分

---

## 本日のアジェンダ

1. **前回の振り返り・課題の確認** (10分)
2. **Source と Layer** (15分)
3. **実習1: 自分の GeoJSON を重ねる** (25分)
4. **地図を動かす仕組み** (10分)
5. **実習2: 触れる地図にする** (25分)
6. **まとめ・課題** (10分)

---

<style scoped>section { font-size: 26px; }</style>

## 前回の振り返り

### 第7回の主要ポイント

- ウェブ地図は **ベースマップ + オーバーレイ + UI** を重ねてできている
- ベースマップは**タイル**に分けられ、見えている分だけが届く（Z/X/Y）
- ウェブ地図は **HTML ファイル**として作り、**ブラウザで開いて**見る
- MapLibre GL JS のコードは AI に書いてもらい、自分で読んで確かめる

### 課題の確認

前回作った地図ページを見せてください

- どのベースマップを選んだか。その理由は？
- AI にどんな指示を出したか。困ったところは？

---

## 今日のゴール

前回のページは**ベースマップだけ**だった

今日は、その上に

1. **自分のデータ**（GeoJSON）を重ねる => オーバーレイ
2. クリックすると**情報が出る**ようにする => UI

=> 仮のテーマの地図の、最初の形ができる

---

## Source と Layer

<div class="cols">
<div>

MapLibre GL JS は、地図を2つに分けて考える

- **Source**（ソース）
  **何を**描くか = データ
  （タイル、GeoJSON など）
- **Layer**（レイヤー）
  **どう**描くか = 見た目
  （丸で描く、線で描く、色、太さなど）

1つの Source から、複数の Layer を作れる

</div>
<svg width="480" height="351" viewBox="0 0 520 380" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="左に Source が2つ、右に Layer が並ぶ図。ベースマップのタイルからはたくさんの Layer が、data.geojson からは circle、line、fill の3つの Layer が作られる">
  <defs><marker id="sl-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#666"/></marker></defs>
  <text x="100" y="22" font-size="20" font-weight="bold" text-anchor="middle" fill="#7a5d00">Source</text>
  <text x="100" y="44" font-size="14" text-anchor="middle" fill="#7a5d00">何を描くか（データ）</text>
  <text x="405" y="22" font-size="20" font-weight="bold" text-anchor="middle" fill="#1d4f8f">Layer</text>
  <text x="405" y="44" font-size="14" text-anchor="middle" fill="#1d4f8f">どう描くか（見た目）</text>
  <rect x="10" y="70" width="180" height="64" rx="8" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="100" y="97" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">ベースマップのタイル</text>
  <text x="100" y="119" font-size="13" text-anchor="middle" fill="#666">type: vector / raster</text>
  <rect x="312" y="60" width="198" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="1.5"/>
  <rect x="306" y="67" width="198" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="1.5"/>
  <rect x="300" y="74" width="198" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <text x="399" y="98" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">道路・建物・地名…</text>
  <text x="399" y="119" font-size="13" text-anchor="middle" fill="#666">たくさんの Layer</text>
  <line x1="190" y1="102" x2="297" y2="102" stroke="#666" stroke-width="2" marker-end="url(#sl-arrow)"/>
  <rect x="10" y="236" width="180" height="64" rx="8" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="100" y="263" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">data.geojson</text>
  <text x="100" y="285" font-size="13" text-anchor="middle" fill="#666">type: geojson</text>
  <rect x="300" y="170" width="210" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <circle cx="328" cy="198" r="8" fill="#e5533d"/>
  <text x="352" y="194" font-size="16" font-weight="bold" fill="#333">circle</text>
  <text x="352" y="214" font-size="13" fill="#666">ポイントを丸で描く</text>
  <rect x="300" y="240" width="210" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <polyline points="314,280 326,258 342,270" fill="none" stroke="#e5533d" stroke-width="3" stroke-linejoin="round" stroke-linecap="round"/>
  <text x="352" y="264" font-size="16" font-weight="bold" fill="#333">line</text>
  <text x="352" y="284" font-size="13" fill="#666">ラインを線で描く</text>
  <rect x="300" y="310" width="210" height="56" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <polygon points="315,350 321,326 342,330 338,352" fill="#f7c9c0" stroke="#e5533d" stroke-width="2" stroke-linejoin="round"/>
  <text x="352" y="334" font-size="16" font-weight="bold" fill="#333">fill</text>
  <text x="352" y="354" font-size="13" fill="#666">ポリゴンを塗りつぶす</text>
  <path d="M190,268 C245,268 245,198 297,198" fill="none" stroke="#666" stroke-width="2" marker-end="url(#sl-arrow)"/>
  <line x1="190" y1="268" x2="297" y2="268" stroke="#666" stroke-width="2" marker-end="url(#sl-arrow)"/>
  <path d="M190,268 C245,268 245,338 297,338" fill="none" stroke="#666" stroke-width="2" marker-end="url(#sl-arrow)"/>
</svg>
</div>

---

## Source の種類

Source の `type` で、データの種類を伝える

| `type` | データ | これまでの授業では |
|--|--|--|
| `raster` | ラスタタイル | 地理院タイル、OpenStreetMap の標準タイル（第7回） |
| `vector` | ベクトルタイル | OpenFreeMap（第7回） |
| `geojson` | GeoJSON | 自分で作ったデータ（第5回） |

今日使うのは **`geojson`**

---

<style scoped>section { font-size: 26px; }</style>

## Layer の種類

Layer の `type` で、描き方を伝える

| ジオメトリ（第2回） | Layer の `type` | 描かれるもの |
|--|--|--|
| ポイント | `circle` | 丸 |
| ポイント | `symbol` | アイコンや文字 |
| ライン | `line` | 線 |
| ポリゴン | `fill` | 塗りつぶし |
| ポリゴン | `line` | 輪郭線 |

- ポイント・ライン・ポリゴンが混ざった GeoJSON は、**ジオメトリごとに Layer を分ける**
- Layer は**後から足したものほど上**に重なる

---

<style scoped>section { font-size: 24px; }</style>

## 観察: 前回のベースマップの中身

MapLibre GL JS の地図には、必ず **`style`** がある（第7回）
`style` は、**どのデータを、どう描くか**をまとめた設定。自分のコードではどちらの形？

**URL で書かれている**（例: OpenFreeMap）=> その URL をブラウザで開くと、中身を読める

```js
style: 'https://tiles.openfreemap.org/styles/liberty',
```

**コードに直接書かれている**（例: 地理院タイル、OpenStreetMap の標準タイル）

```js
style: { version: 8, sources: { ... }, layers: [ ... ] },
```

- `sources` と `layers` という言葉は見つかる？
- `layers` には、Layer がいくつくらいある？

=> ベースマップも Source と Layer でできている。自分のデータは、その上に Layer を足す

---

<style scoped>section { font-size: 24px; }</style>

## コードではこう書かれる

```js
// map は、前回の new maplibregl.Map(...) で作った地図
map.on('load', () => {
  // Source: 何を描くか
  map.addSource('my-data', {
    type: 'geojson',
    data: './data.geojson',
  })

  // Layer: どう描くか
  map.addLayer({
    id: 'my-points',
    type: 'circle',
    source: 'my-data',
    paint: { 'circle-radius': 6, 'circle-color': '#e5533d' },
  })
})
```

書き方は覚えなくて OK。**`addSource` と `addLayer` を見つけて、読める**ようになろう

---

<style scoped>section { font-size: 24px; }</style>

## 実習1: 自分の GeoJSON を重ねる（25分）

### ゴール

前回のページのベースマップの上に、自分の GeoJSON を表示する

### 使うデータ

仮のテーマに合うものを1つ選ぶ

- 第5回で作った GeoJSON
- 第4回のように Overpass Turbo で取り出した GeoJSON
- [geojson.io](https://geojson.io/) で新しく作った GeoJSON

### 進め方

1. データを置く → 2. AI に頼む → 3. ローカルサーバーで開く → 4. コードを読む
---

## ステップ1・2: データを置いて、AI に頼む

1. GeoJSON ファイルを、前回の HTML と**同じフォルダ**に置く
   （例: `data.geojson`）
2. AI に頼む

```text
この地図に、同じフォルダにある data.geojson を重ねて表示してください。
- ポイントは丸、ラインは線、ポリゴンは塗りつぶしで描く
- 色は1色でよい
- 最初に、データ全体が画面に収まるように表示する
```

色やサイズの作り込みは次回。今日は**表示されること**を目指す

---

<style scoped>section { font-size: 24px; }</style>

## ステップ3: ローカルサーバーで開く

前回のように HTML ファイルをそのまま開く（`file://...`）と、データが出ないことが多い

<div class="cols">
<div>

- ブラウザは安全のため、`file://` で開いたページが**他のファイルを読み込むこと**を制限している
- => 自分のパソコンの中で小さなウェブサーバー（**ローカルサーバー**）を動かし、`http://localhost...` で開く

**やり方**: AI に「このフォルダをローカルサーバーで開きたい」と頼む

- 例: エディタの拡張機能 **Live Server**
- 例: ターミナルで `python3 -m http.server`

</div>
<svg width="500" height="330" viewBox="0 0 500 330" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="上は file:// でそのまま開いた場合で、ブラウザから data.geojson を読み込めない。下はローカルサーバーで開いた場合で、ブラウザがサーバーにリクエストを送り、index.html と data.geojson を受け取る">
  <defs>
    <marker id="ls-red" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#b3261e"/></marker>
    <marker id="ls-green" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#3c8d3c"/></marker>
  </defs>
  <text x="0" y="18" font-size="16" font-weight="bold" fill="#b3261e">✕ そのまま開く</text>
  <rect x="1" y="32" width="200" height="84" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <rect x="11" y="42" width="180" height="24" rx="4" fill="#fff" stroke="#999"/>
  <text x="18" y="59" font-size="12" font-family="monospace" fill="#b3261e">file:///.../index.html</text>
  <text x="101" y="96" font-size="15" text-anchor="middle" fill="#333">ブラウザ</text>
  <rect x="330" y="44" width="168" height="60" rx="8" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="414" y="80" font-size="15" font-family="monospace" text-anchor="middle" fill="#333">data.geojson</text>
  <line x1="201" y1="74" x2="327" y2="74" stroke="#b3261e" stroke-width="2" stroke-dasharray="6 4" marker-end="url(#ls-red)"/>
  <text x="264" y="66" font-size="13" text-anchor="middle" fill="#b3261e">読み込めない</text>
  <text x="264" y="100" font-size="24" font-weight="bold" text-anchor="middle" fill="#b3261e">✕</text>
  <line x1="0" y1="146" x2="500" y2="146" stroke="#ccc" stroke-width="1"/>
  <text x="0" y="182" font-size="16" font-weight="bold" fill="#3c8d3c">○ ローカルサーバーで開く</text>
  <rect x="1" y="196" width="200" height="84" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <rect x="11" y="206" width="180" height="24" rx="4" fill="#fff" stroke="#999"/>
  <text x="18" y="223" font-size="12" font-family="monospace" fill="#3c8d3c">http://localhost:8000</text>
  <text x="101" y="260" font-size="15" text-anchor="middle" fill="#333">ブラウザ</text>
  <rect x="310" y="196" width="188" height="130" rx="8" fill="#e3f4e1" stroke="#3c8d3c" stroke-width="2"/>
  <text x="404" y="220" font-size="15" font-weight="bold" text-anchor="middle" fill="#2c6b2c">ローカルサーバー</text>
  <rect x="324" y="232" width="160" height="34" rx="4" fill="#fff" stroke="#999"/>
  <text x="404" y="254" font-size="14" font-family="monospace" text-anchor="middle" fill="#333">index.html</text>
  <rect x="324" y="276" width="160" height="34" rx="4" fill="#fff" stroke="#999"/>
  <text x="404" y="298" font-size="14" font-family="monospace" text-anchor="middle" fill="#333">data.geojson</text>
  <line x1="201" y1="226" x2="307" y2="226" stroke="#3c8d3c" stroke-width="2" marker-end="url(#ls-green)"/>
  <text x="255" y="218" font-size="12" text-anchor="middle" fill="#2c6b2c">ください</text>
  <line x1="310" y1="256" x2="204" y2="256" stroke="#3c8d3c" stroke-width="2" marker-end="url(#ls-green)"/>
  <text x="255" y="274" font-size="12" text-anchor="middle" fill="#2c6b2c">どうぞ</text>
</svg>
</div>

---

## ステップ4: 生成されたコードを読む

- `addSource` はどこ？ `type` と `data` には何が書かれている？
- `addLayer` はいくつある？ それぞれの `type` は？
- 自分のデータのジオメトリと、Layer の `type` は合っている？
- 色や大きさは、どこで決まっている？（`paint` を探す）
- 数字や色を1つ書き換えて、地図が変わるのを確かめる

わからない行は、AI に「この行は何をしている？」と聞く

---

## うまくいかないとき

| 症状 | 確かめること |
|--|--|
| ベースマップは出るが、データが出ない | `http://localhost...` で開いているか。Console のエラーを AI に貼る |
| データが海の上に出る | GeoJSON の座標が（経度, 緯度）の順か（第5回） |
| ポリゴンの角に丸が出る | `circle` の Layer がポイント以外も描いている。ポイントだけに絞ってもらう |
| データが小さすぎて見えない | データ全体が収まるように表示してもらう |

---

<style scoped>section { font-size: 24px; }</style>

## 地図を動かす仕組み

ここまでの地図は、見るだけの地図
触れる地図にするために、AI に頼むときの**3つの言葉**を知っておく

| 言葉 | 意味 | 例 |
|--|--|--|
| **コントロール** | 地図の上に置く部品 | ズームボタン、縮尺、現在地ボタン |
| **イベント** | 利用者の操作をきっかけに何かをする仕組み | クリックしたら情報を出す |
| **カメラ** | 地図の中心・ズーム・向き・傾き | ボタンを押したらその場所へ移動する |

---

<style scoped>section { font-size: 24px; }</style>

## イベント: クリックしたら情報を出す

<svg width="1000" height="150" viewBox="0 0 1000 150" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="1. 地図上の点をクリックする。2. その地物の properties を取り出す。3. ポップアップに表示する、という流れの図">
  <defs><marker id="ev-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#666"/></marker></defs>
  <text x="130" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">1. 地物をクリックする</text>
  <rect x="1" y="30" width="258" height="118" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <line x1="1" y1="100" x2="259" y2="80" stroke="#ccc" stroke-width="6"/>
  <line x1="90" y1="30" x2="110" y2="148" stroke="#ccc" stroke-width="6"/>
  <circle cx="60" cy="60" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="215" cy="60" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="150" cy="110" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <polygon points="150,110 150,138 157,132 162,144 167,142 162,130 171,130" fill="#fff" stroke="#333" stroke-width="1.5" stroke-linejoin="round"/>
  <line x1="272" y1="89" x2="357" y2="89" stroke="#666" stroke-width="2" marker-end="url(#ev-arrow)"/>
  <text x="500" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">2. properties を取り出す</text>
  <rect x="371" y="30" width="258" height="118" rx="8" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="387" y="62" font-size="14" font-family="monospace" fill="#333">{</text>
  <text x="405" y="84" font-size="14" font-family="monospace" fill="#333">"name": "図書館",</text>
  <text x="405" y="106" font-size="14" font-family="monospace" fill="#333">"amenity": "library"</text>
  <text x="387" y="128" font-size="14" font-family="monospace" fill="#333">}</text>
  <line x1="642" y1="89" x2="727" y2="89" stroke="#666" stroke-width="2" marker-end="url(#ev-arrow)"/>
  <text x="870" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">3. ポップアップに表示する</text>
  <rect x="741" y="30" width="258" height="118" rx="8" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <line x1="741" y1="100" x2="999" y2="80" stroke="#ccc" stroke-width="6"/>
  <line x1="830" y1="30" x2="850" y2="148" stroke="#ccc" stroke-width="6"/>
  <circle cx="800" cy="60" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="955" cy="60" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="890" cy="110" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <polygon points="882,94 898,94 890,104" fill="#fff" stroke="#999" stroke-width="1"/>
  <rect x="845" y="56" width="90" height="39" rx="5" fill="#fff" stroke="#999" stroke-width="1"/>
  <text x="890" y="81" font-size="15" font-weight="bold" text-anchor="middle" fill="#333">図書館</text>
</svg>

```js
map.on('click', 'my-points', (e) => {
  const properties = e.features[0].properties
  new maplibregl.Popup()
    .setLngLat(e.lngLat)
    .setHTML(properties.name)
    .addTo(map)
})
```

- 「`my-points` の Layer が**クリックされたら**、〜する」と読む
- クリックされた地物の **`properties`**（第5回）を取り出して、**ポップアップ**に表示している

---

## 実習2: 触れる地図にする（25分）

### ゴール

データをクリックすると、`properties` の内容がポップアップで出るようにする

### AI への頼み方の例

```text
データをクリックしたら、properties の name と description を
ポップアップで表示してください。
データの上にマウスを置いたら、カーソルを指の形にしてください。
```

`name` や `description` は、自分の GeoJSON の `properties` に合わせて書き換える

---

## できたら試してみよう

1つずつ頼んで、動くのを確かめながら進める

- **コントロール**を足す: ズームボタン、縮尺、現在地ボタン、全画面ボタン
- **カメラ**を動かす: ボタンを押したら、特定の場所へなめらかに移動する
- ポップアップに `properties` の複数の項目を見やすく並べる
- 地図の向きや傾きを変えてみる（右ドラッグ、または `Ctrl` + ドラッグ）

---

## コードを読む: イベントとコントロール

- `map.on(` を探す。**何が起きたとき**に、**何をする**と書いてある？
- ポップアップに出しているのは、`properties` のどの項目？
- `addControl` を探す。どんなコントロールが、地図の**どの隅**に置かれている？
- `properties` に項目を1つ足したら、ポップアップにも出せる？

---

## 自分のデータにも出典表記を

ベースマップだけでなく、**重ねたデータ**にも出典がある

- Overpass Turbo で取り出したデータ
  => `© OpenStreetMap contributors`（第3-4回）
- 自治体や国のオープンデータ
  => それぞれの利用規約に従う
- 自分で作ったデータ
  => 表記は不要（書いてもよい）

AI に「この GeoJSON の出典表記を地図に足して」と頼めば、Source に `attribution` を足してくれる

---

## まとめ

- MapLibre GL JS は、**Source**（何を描くか）と **Layer**（どう描くか）で地図を作る
- Layer の `type` はジオメトリに合わせる（ポイントは `circle`、ラインは `line`、ポリゴンは `fill`）
- GeoJSON を読み込むページは、**ローカルサーバー**で開く
- **イベント**で、クリックなどの操作に反応する地図になる
- コードの中の `addSource` / `addLayer` / `map.on` / `addControl` を見つけて読む

---

<div class="assignment">

## 課題: 自分のデータを重ねた、触れる地図を作る

### 課題内容

前回の地図ページに、仮のテーマのデータを重ねてください。

- 自分の GeoJSON が、ベースマップの上に表示されること
- データをクリックすると、`properties` の内容がポップアップで出ること
- コントロールを1つ以上置くこと
- ベースマップと、重ねたデータの出典表記が表示されていること

</div>

---

<style scoped>section { font-size: 26px; }</style>

<div class="assignment">

### 提出要項

- **提出物**:
  - 作ったファイル一式（zip にまとめる。GitHub リポジトリの URL でも可）
  - ポップアップを表示した画面のスクリーンショット
  - メモ: 使ったデータとその出典 / AI に出した主な指示 / 生成されたコードの Source と Layer について確認したこと・直したこと
- **提出期限**: 次回授業開始時
- **提出方法**: moodle+R 経由
- **評価ポイント**: データが表示されること、ポップアップ、出典表記、生成されたコードを自分で確かめられているか

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

**第9回: スタイルの基本（ポイント、ライン、ポリゴン）**
