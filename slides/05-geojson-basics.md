---
marp: true
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

## 第5回: GeoJSON の基礎

立命館大学 2026年度 秋セメスター 火曜5限
授業時間: 95分

---

## 本日のアジェンダ

1. **前回の振り返り・課題確認** (10分)
2. **データ交換フォーマットと JSON** (25分)
3. **GeoJSONフォーマットの理解・構造と記法** (28分)
4. **簡単なGeoJSONファイルの作成** (20分)
5. **地図上での可視化方法・課題説明** (12分)

---

## 前回の振り返り

### 第3-4回の主要ポイント

- ライセンス: OSM のデータは ODbL。使うときは **© OpenStreetMap contributors**
- OSM はコミュニティが現地調査や航空写真のトレースで作っている
- iD エディタで OSM を編集してみた
- **Overpass Turbo** で条件を指定してデータを取得し、**GeoJSON** でエクスポートした

### 課題の確認

- Overpass Turbo で興味のあるエリアのデータを調査する（小レポート）

---

## データ交換フォーマット

GeoJSON は JSON フォーマットで位置情報を表すもの。JSON はウェブシステムなどの文脈では「データ交換フォーマット」とも呼ばれる

### 考えてみよう

普段使っているスマホのアプリは、サーバーとどんなデータをやりとりしているだろうか？

- サーバーから**受け取っている**データは？
- サーバーへ**送っている**データは？

---

### データ交換フォーマットの役割

#### 取得（サーバー -> アプリ）
- 天気アプリ -> 気象庁のサーバーからデータを取得
- 地図アプリ -> 地図データサーバーからデータを取得
- SNSアプリ -> 友達の投稿データを取得

#### 送信（アプリ -> サーバー）
- SNSアプリ -> 自分の投稿（文章・写真・位置情報）を送信
- 地図アプリ -> お店のレビューや口コミを送信
- ランニングアプリ -> 走ったルート（GPS の記録）をアップロード

アプリ <-> サーバー間でデータを送り合う共通のフォーマットが必要
JSON、 XML、 CSV、 etc.

---

## JSON - **J**ava**S**cript **O**bject **N**otation

#### 特徴

- テキスト形式
- ブラウザで標準的にサポート、プログラム (JavaScript)での解析が容易

#### 他のデータ交換フォーマット

- **XML**: より複雑なデータ構造を表現可能。GPX など
- **YAML**: 人間が読みやすい
- **CSV**: 表形式データに特化

---

## JSONサンプル

```json
[
  {
    "name": "喫茶さくら",
    "category": "cafe",
    "seats": 20,
    "menu": ["コーヒー", "ホットケーキ"],
    "location": { "pref": "京都府", "city": "京都市" }
  },
  {
    "name": "湖畔食堂",
    "category": "restaurant",
    "seats": 35,
    "menu": ["焼き魚定食", "うどん"],
    "location": { "pref": "滋賀県", "city": "大津市" }
  }
]
```

---

## XML サンプル

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- お店のリスト: これはコメント -->
<shops>
  <shop name="喫茶さくら" category="cafe" seats="20" location_pref="京都府" location_city="京都市">
    <item name="コーヒー"/>
    <item name="ホットケーキ"/>
  </shop>
  <shop name="湖畔食堂" category="restaurant" seats="35" location_pref="滋賀県" location_city="大津市">
    <item name="焼き魚定食"/>
    <item name="うどん"/>
  </shop>
</shops>
```

---

### XML の構成要素

XML を構成するもの:
- 要素 ex. `<shop></shop>`
- 属性 ex. `name="喫茶さくら"`
- XML はデータ型を持たない。`seats="20"` の `20` が数字か文字列かは区別できない

---

<style scoped>pre { font-size: 20px; }</style>

## YAML

```yaml
# お店のリスト <- これはコメント
shops:
  - name: 喫茶さくら
    category: cafe
    seats: 20
    menu:
      - コーヒー
      - ホットケーキ
    location:
      pref: 京都府
      city: 京都市

  - name: 湖畔食堂
    category: restaurant
    seats: 35
    menu:
      - 焼き魚定食
      - うどん
    location:
      pref: 滋賀県
      city: 大津市
```

---

## CSV

```csv
name,category,seats,menu_1,menu_2,location_pref,location_city
喫茶さくら,cafe,20,コーヒー,ホットケーキ,京都府,京都市
湖畔食堂,restaurant,35,焼き魚定食,うどん,滋賀県,大津市
```

| `name` | `category` | `seats` | `menu_1` | `menu_2` | `location_pref` | `location_city` |
|------|----------|-----|-------------|-------------|----------------|----------------|
| 喫茶さくら | cafe | 20 | コーヒー | ホットケーキ | 京都府 | 京都市 |
| 湖畔食堂 | restaurant | 35 | 焼き魚定食 | うどん | 滋賀県 | 大津市 |

---

## 巨大なデータを扱う

以下の形式の JSON が **100万件** あったら、ファイルサイズはどのくらい？

```text
[
  {"name":"喫茶さくら","seats":20},
  ...
]
```

### まず予想して、書き留めておこう

A. 約 4MB　B. 約 40MB　C. 約 400MB　D. 約 4GB

---

### 実習: 計算してみよう（5分）

ヒント: アスキー文字（a-z, 0-9, 記号）は **1 byte**、日本語は **3 byte** / 文字

| 部分 | 文字数 | byte |
|:--|--:|--:|
| `{"name":"` | 9 | ＿＿ |
| `喫茶さくら` | 5 | ＿＿ |
| `","seats":` | 10 | ＿＿ |
| `20` | 2 | ＿＿ |
| `},` + 改行 | 3 | ＿＿ |
| **1件あたり** | | **＿＿** |

1件あたり ＿＿ byte × 100万件 = ＿＿ byte ≈ ＿＿ MB（1MB = 1024 × 1024 byte）

---

### 答え合わせ

| 部分 | 文字数 | byte |
|:--|--:|--:|
| `{"name":"` | 9 | 9 |
| `喫茶さくら` | 5 | 15 |
| `","seats":` | 10 | 10 |
| `20` | 2 | 2 |
| `},` + 改行 | 3 | 3 |
| **1件あたり** | | **39** |

39 byte × 100万件 = 39,000,000 byte ≈ **約 37MB**（答えは B）

---

### 確かめてみよう: ブラウザのコンソール

ブラウザで開発者ツールを開く（Chrome: `F12` または `Cmd + Option + J`）→ Console に貼り付ける

```js
const shops = Array.from({ length: 1000000 }, () => ({ name: "喫茶さくら", seats: 20 }));
const json = JSON.stringify(shops);
new TextEncoder().encode(json).length / 1024 / 1024; // MB
```

- 計算と比べてどうだった？（`JSON.stringify` は改行を入れないので、少し小さくなる）
- **発展**: `{ name: ..., seats: ... }` にプロパティを足して、10個・100個にしたら何 MB になるか試してみよう

---

### まとめ

- プロパティ1個あたり約 15 byte（`"key":"value",`）とすると
  - 10個 → 約 150 byte/件 ≈ **約 143MB**
  - 100個 → 約 1.5 kB/件 ≈ **約 1.4GB**

JSON は**キー名を1件ごとに毎回書く**ので、データが増えるとサイズが膨らみやすい
（CSV はキー名＝列名を先頭行に1回書くだけ）

---

## CSV は大きなデータを扱いやすい

- CSV = カンマ区切りの表形式データ（1行が1つのデータのまとまり。例：名前,年齢,住所）
- 逐次読み込みが簡単
  - 上から1行ずつ「読みながら処理 → 終わったら忘れる」でメモリを節約
- YAML / XML / JSON は階層構造（ネスト・入れ子）を持つ
  - 1つのまとまりを理解するには、全体を一度に読み込む必要がある場合が多い

### 結果

- CSV：少ないメモリで大容量データも扱いやすい
- XML / JSON / YAML：便利だがコンピューターのメモリを多く使いやすい

---

## NDJSON - **N**ewline **D**elimited JSON (改行区切り JSON)

- JSON にはない CSV のメリットとして、1行ずつの逐次処理がしやすい点があった
- では、 JSON を1行ずつ処理できるようにするには？

```ndjson
{ "name": "喫茶さくら", "seats": 20 }
{ "name": "湖畔食堂", "seats": 35 }
...
```

=> 1行に1つの JSON を書けば、CSV と同じように1行ずつ読んで処理できる

---

### 表現力の差

||JSON|YAML|XML|CSV|
|:--:|:--:|:--:|:--:|:--:|
|入れ子構造|○|○|○|×|
|データ型|○|○|×|×|
|コメントサポート|×|○|○|×|
|可読性|△|○|×|○|

用途（ウェブ API、設定ファイル、表計算など）や既存システムとの互換性によって、適したフォーマットが異なる

---

## JSON の基本データ型

### プリミティブ型（基本型）

| 型 | 例 |
|--|--|
| 数値型 | `42` `3.14159` `-10` |
| 文字列型 | `"Hello, World!"` `"立命館大学"` `"2026-10-27"` |
| 真偽値型 | `true` `false` |
| null型 | `null` |

**重要**: 文字列は必ずダブルクオーテーション `""` で囲む

---

## JSON の配列型

```json
["りんご", "みかん", "バナナ"]
[1, 2, 3, 4, 5]
[true, false, null, 42, "文字列"]
```

#### 特徴
- `[]` 角かっこで囲む
- 要素はカンマ `,` で区切る
- 異なる型の要素を混在可能
- 配列の中に配列やオブジェクトも入れられる

---

### 実習: 配列を書いてみよう

あなたの好きな食べ物トップ3を JSON の配列形式で書いて、ブラウザのコンソールで確かめよう。

```js
JSON.parse(`["ラーメン", "寿司", "カレー"]`)
```

- 正しければ配列が表示される。間違っていると `SyntaxError` が出る
- `` ` `` はバッククオート（JIS キーボードでは `Shift + @`）

---

## JSON のオブジェクト型

```json
{
  "name": "田中太郎",
  "age": 20,
  "student": true,
  "university": "立命館大学",
  "hobbies": ["読書", "映画鑑賞", "プログラミング"]
}
```

#### 特徴
- `{}` 波かっこで囲み、`"キー": 値` のペアをカンマ `,` で区切る
- キーは必ず文字列（ダブルクオーテーションで囲む）
- 値は任意の JSON データ型（プリミティブ型、配列型、オブジェクト型）
- 改行・インデントは自由: `{ "店名": "喫茶さくら", "席数": 20 }` でもよい

---

### 実習: オブジェクトを書いてみよう

今日のお昼ご飯を JSON のオブジェクト形式で表現して、コンソールで確かめよう。
メニュー名と値段をキーとして使ってください。

```js
JSON.parse(`{"メニュー": "親子丼", "値段": 800}`)
```

---

<style scoped>section { font-size: 24px; }</style>

## JSON 記法のルール

#### 1. 文字列は必ずダブルクオーテーション

✅ 正しい: `"Hello"`
❌ 間違い: `'Hello'`、`Hello`

#### 2. 最後の要素の後にカンマは不要

✅ 正しい
```json
["a", "b", "c"]
{"x": 1, "y": 2}
```

❌ 間違い
```text
["a", "b", "c",]
{"x": 1, "y": 2,}
```

※ JSON にはコメントも書けない（`// ...` や `/* ... */` は不可）

---

### JSON 理解度チェック

以下の JSON のうち、**文法的に正しくない**ものを**すべて**選んでください。

A.
```json
{ "name": "田中太郎", "age": 20, "hobbies": ["読書", "映画"] }
```

B.
```json
{ 'name': '田中太郎', 'age': 20, 'hobbies': ['読書', '映画'] }
```

C.
```json
{ name: "田中太郎", age: 20, hobbies: ["読書", "映画",] }
```

---

### 答え: B と C

- **A**: ✅ 正しい
- **B**: ❌ 文字列とキーがシングルクオーテーション `'...'`
- **C**: ❌ キーがクオーテーションで囲まれていない、配列の最後にカンマ `"映画",`

---

<style scoped>pre { font-size: 16px; }</style>

### コンソールで確かめよう

<div class="cols">
<div>

B と C を `JSON.parse` に入れると `SyntaxError` になる

```js
JSON.parse(`{ name: "田中太郎", hobbies: ["読書",] }`) // SyntaxError
```

一方、`JSON.parse` を使わずにそのまま入力すると、エラーにならない

```js
{ name: "田中太郎", hobbies: ["読書",] } // OK
```

</div>
<svg width="400" height="300" viewBox="0 0 400 300" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="JavaScript の書き方の中に JSON の書き方が含まれる包含図">
  <rect x="4" y="4" width="392" height="292" rx="24" fill="#fff4d6" stroke="#c99a00" stroke-width="3"/>
  <text x="24" y="40" font-size="22" font-weight="bold" fill="#7a5d00">JavaScript</text>
  <text x="24" y="270" font-size="16" fill="#7a5d00">{ name: '太郎', }</text>
  <rect x="60" y="70" width="280" height="150" rx="20" fill="#dcecff" stroke="#2f6fbf" stroke-width="3"/>
  <text x="80" y="104" font-size="22" font-weight="bold" fill="#1d4f8f">JSON</text>
  <text x="80" y="170" font-size="16" fill="#1d4f8f">{ "name": "太郎" }</text>
</svg>
</div>

=> JSON は JavaScript の**サブセット**（JavaScript は JSON の**スーパーセット**）。JavaScript の書き方を「データ交換」用に制限したもの

---

## GeoJSONフォーマットの理解

### GeoJSON とは？

#### 定義

> GeoJSON は空間データを JSON形式で表現するためのフォーマット

- 実習: [geojson.io](https://geojson.io) で点・線・面を描いて、右側に表示される GeoJSON を観察してみる
- 前回 Overpass Turbo でエクスポートした `export.geojson` も GeoJSON！

用語: feature = 地物

---

## GeoJSONの構造と記法

### 基本構造

<div class="cols">
<div>

#### GeoJSON オブジェクトの種類
1. **Geometry**: 幾何学的形状
2. **Feature**: 地物（ジオメトリ + 属性）
3. **FeatureCollection**: 複数の Feature をまとめたもの

</div>
<svg width="380" height="330" viewBox="0 0 380 330" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="FeatureCollection の中に複数の Feature があり、各 Feature は geometry と properties を持つ入れ子の図">
  <rect x="4" y="4" width="372" height="322" rx="16" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <text x="20" y="34" font-size="20" font-weight="bold" fill="#333">FeatureCollection</text>
  <rect x="24" y="56" width="332" height="122" rx="12" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="40" y="84" font-size="18" font-weight="bold" fill="#7a5d00">Feature</text>
  <rect x="40" y="98" width="140" height="64" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <text x="52" y="124" font-size="17" fill="#1d4f8f">geometry</text>
  <text x="52" y="149" font-size="14" fill="#1d4f8f">形と座標</text>
  <rect x="196" y="98" width="144" height="64" rx="8" fill="#e3f4e1" stroke="#3c8d3c" stroke-width="2"/>
  <text x="208" y="124" font-size="17" fill="#2b6a2b">properties</text>
  <text x="208" y="149" font-size="14" fill="#2b6a2b">名前などの属性</text>
  <rect x="24" y="190" width="332" height="122" rx="12" fill="#fff4d6" stroke="#c99a00" stroke-width="2"/>
  <text x="40" y="218" font-size="18" font-weight="bold" fill="#7a5d00">Feature</text>
  <rect x="40" y="232" width="140" height="64" rx="8" fill="#dcecff" stroke="#2f6fbf" stroke-width="2"/>
  <text x="52" y="258" font-size="17" fill="#1d4f8f">geometry</text>
  <text x="52" y="283" font-size="14" fill="#1d4f8f">形と座標</text>
  <rect x="196" y="232" width="144" height="64" rx="8" fill="#e3f4e1" stroke="#3c8d3c" stroke-width="2"/>
  <text x="208" y="258" font-size="17" fill="#2b6a2b">properties</text>
  <text x="208" y="283" font-size="14" fill="#2b6a2b">名前などの属性</text>
</svg>
</div>

---

### 共通ルール

- すべての GeoJSON オブジェクトは `type` を必ず持つ
- 例: FeatureCollection は `type` と `features` が必須

```text
{
  "type": "FeatureCollection",
  "features": [...]
}
```

---

### Geometry オブジェクト

<div class="cols">
<div>

#### Point（点）
```json
{
  "type": "Point",
  "coordinates": [135.5, 34.7]
}
```

</div>
<svg width="240" height="150" viewBox="0 0 240 150" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="Point は1つの点であることを示す図">
  <rect x="2" y="2" width="236" height="146" rx="12" fill="#f7f9fc" stroke="#ccd" stroke-width="2"/>
  <text x="16" y="30" font-size="18" font-weight="bold" fill="#333">Point</text>
  <circle cx="120" cy="86" r="9" fill="#2f6fbf"/>
  <text x="120" y="122" font-size="14" text-anchor="middle" fill="#333">[135.5, 34.7]</text>
</svg>
</div>

---

#### LineString（線）

<div class="cols">
<div>

```json
{
  "type": "LineString",
  "coordinates": [
    [135.5, 34.7],
    [135.6, 34.8],
    [135.7, 34.9]
  ]
}
```

</div>
<svg width="240" height="250" viewBox="0 170 240 250" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="LineString は3つの点を順につないだ線であることを示す図">
  <rect x="2" y="172" width="236" height="246" rx="12" fill="#f7f9fc" stroke="#ccd" stroke-width="2"/>
  <text x="16" y="200" font-size="18" font-weight="bold" fill="#333">LineString</text>
  <polyline points="50,380 120,310 190,240" fill="none" stroke="#2f6fbf" stroke-width="4"/>
  <circle cx="50" cy="380" r="6" fill="#2f6fbf"/>
  <circle cx="120" cy="310" r="6" fill="#2f6fbf"/>
  <circle cx="190" cy="240" r="6" fill="#2f6fbf"/>
  <text x="60" y="404" font-size="13" text-anchor="middle" fill="#333">[135.5, 34.7]</text>
  <text x="130" y="332" font-size="13" fill="#333">[135.6, 34.8]</text>
  <text x="190" y="226" font-size="13" text-anchor="middle" fill="#333">[135.7, 34.9]</text>
</svg>
</div>

---

#### Polygon（面）

<div class="cols">
<div>

```json
{
  "type": "Polygon",
  "coordinates": [
    [
      [135.5, 34.7],
      [135.6, 34.7],
      [135.6, 34.8],
      [135.5, 34.8],
      [135.5, 34.7]
    ]
  ]
}
```

</div>
<svg width="300" height="260" viewBox="0 0 300 260" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="正方形の4つの角に1から4の番号があり、1から反時計回りに進んで5番目で1に戻る図">
  <defs>
    <marker id="arr-poly" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="5" markerHeight="5" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#1d4f8f"/></marker>
  </defs>
  <rect x="60" y="50" width="180" height="160" fill="#dcecff" stroke="#2f6fbf" stroke-width="3"/>
  <line x1="138" y1="210" x2="162" y2="210" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-poly)"/>
  <line x1="240" y1="142" x2="240" y2="118" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-poly)"/>
  <line x1="162" y1="50" x2="138" y2="50" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-poly)"/>
  <line x1="60" y1="118" x2="60" y2="142" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-poly)"/>
  <text x="52" y="234" font-size="18" text-anchor="end" fill="#333">1・5</text>
  <text x="248" y="234" font-size="18" fill="#333">2</text>
  <text x="248" y="42" font-size="18" fill="#333">3</text>
  <text x="52" y="42" font-size="18" text-anchor="end" fill="#333">4</text>
  <text x="150" y="136" font-size="16" text-anchor="middle" fill="#1d4f8f">反時計回り</text>
</svg>
</div>

---

#### Polygon（面）の座標

**注意**: Polygon の座標は三重配列
- 1段目: リングの配列（外周 + 穴）
- 2段目: リング = 座標点の配列。最初と最後の座標は同じ（閉じた形状）
- 3段目: 座標点 `[経度, 緯度]`
- 外周は反時計回り、穴は時計回りに並べる（RFC 7946 の推奨）

---

<style scoped>pre { font-size: 20px; }</style>

### ドーナツ(参考)

<div class="cols">
<div>

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [ [-10, -10], [ 10, -10], [ 10,  10], [-10,  10], [-10, -10] ],
          [ [-5,   -5], [ -5,   5], [ 5,    5], [  5,  -5], [-5,  -5 ] ]
        ]
      }
    }
  ]
}
```

</div>
<svg width="260" height="290" viewBox="0 0 260 290" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="外周の正方形が反時計回り、中央の穴の正方形が時計回りに並ぶドーナツ型ポリゴンの図">
  <defs>
    <marker id="arr-donut" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="5" markerHeight="5" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#1d4f8f"/></marker>
  </defs>
  <path d="M30,30 H230 V230 H30 Z M80,80 V180 H180 V80 Z" fill="#dcecff" fill-rule="evenodd" stroke="#2f6fbf" stroke-width="3"/>
  <line x1="118" y1="230" x2="142" y2="230" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="230" y1="142" x2="230" y2="118" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="142" y1="30" x2="118" y2="30" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="30" y1="118" x2="30" y2="142" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="80" y1="142" x2="80" y2="118" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="118" y1="80" x2="142" y2="80" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="180" y1="118" x2="180" y2="142" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <line x1="142" y1="180" x2="118" y2="180" stroke="#1d4f8f" stroke-width="3" marker-end="url(#arr-donut)"/>
  <text x="130" y="136" font-size="18" text-anchor="middle" fill="#333">穴</text>
  <text x="130" y="270" font-size="15" text-anchor="middle" fill="#333">外周: 反時計回り / 穴: 時計回り</text>
</svg>
</div>

---

### 複合 Geometry

<div class="cols">
<div>

#### MultiPoint
```json
{
  "type": "MultiPoint",
  "coordinates": [
    [135.5, 34.7],
    [135.6, 34.8]
  ]
}
```

</div>
<div>

#### MultiLineString
```json
{
  "type": "MultiLineString",
  "coordinates": [
    [[135.5, 34.7], [135.6, 34.8]],
    [[135.7, 34.9], [135.8, 35.0]]
  ]
}
```

</div>
</div>

---

#### MultiPolygon
```json
{
  "type": "MultiPolygon",
  "coordinates": [
    [[[135.5, 34.7], [135.6, 34.7], [135.6, 34.8], [135.5, 34.8], [135.5, 34.7]]],
    [[[135.8, 35.0], [135.9, 35.0], [135.9, 35.1], [135.8, 35.1], [135.8, 35.0]]]
  ]
}
```

---

<style scoped>pre { font-size: 20px; }</style>

### Feature オブジェクト

```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [135.4959, 34.7024]
  },
  "properties": {
    "name": "大阪駅",
    "category": "railway_station",
    "operator": "JR西日本"
  }
}
```

- `properties` には `name`、`description`、`category` など任意のキーを自由に定義できる
- ただし `type` は GeoJSON 自体の `"type": "Feature"` と紛らわしいので避けるのが無難

---

### FeatureCollection オブジェクト

#### 複数の Feature をまとめる
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [135.4959, 34.7024]},
      "properties": {"name": "大阪駅"}
    },
    {
      "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [135.4983, 34.7031]},
      "properties": {"name": "梅田駅"}
    }
  ]
}
```

---

### 座標の順序

<div class="cols">
<div>

#### GeoJSON の標準
```text
[経度, 緯度, 標高（オプション）]
```

#### 注意点
- **経度が先、緯度が後**
- Google Maps など「緯度, 経度」の順で表示するサービスも多いので注意

#### 例: 大阪駅の座標（経度 135.4959, 緯度 34.7024）
```json
[135.4959, 34.7024]
```

</div>
<svg width="320" height="270" viewBox="0 0 320 270" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="横軸が経度、縦軸が緯度の座標平面に大阪駅の点がある図。経度が x、緯度が y にあたる">
  <defs>
    <marker id="arr-axis" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="5" markerHeight="5" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#333"/></marker>
  </defs>
  <line x1="80" y1="220" x2="300" y2="220" stroke="#333" stroke-width="2" marker-end="url(#arr-axis)"/>
  <line x1="80" y1="220" x2="80" y2="24" stroke="#333" stroke-width="2" marker-end="url(#arr-axis)"/>
  <text x="304" y="252" font-size="15" text-anchor="end" fill="#333">経度 = x（東西）</text>
  <text x="90" y="24" font-size="15" fill="#333">緯度 = y（南北）</text>
  <line x1="200" y1="96" x2="200" y2="220" stroke="#999" stroke-width="1.5" stroke-dasharray="4 4"/>
  <line x1="80" y1="96" x2="200" y2="96" stroke="#999" stroke-width="1.5" stroke-dasharray="4 4"/>
  <circle cx="200" cy="96" r="8" fill="#2f6fbf"/>
  <text x="212" y="88" font-size="16" fill="#1d4f8f">大阪駅</text>
  <text x="200" y="238" font-size="13" text-anchor="middle" fill="#333">135.4959</text>
  <text x="72" y="100" font-size="13" text-anchor="end" fill="#333">34.7024</text>
</svg>
</div>

---

### GeoJSON vs 他の地理空間データ形式

| 形式 | 特徴 | 用途 |
|------|------|------|
| **GeoJSON** | JSON ベース（テキスト）、Web標準 | Web地図、API |
| **KML** | XML ベース（テキスト）、OGC 標準 | Google Earth |
| **GPX** | XML ベース（テキスト）、GPS データ交換の事実上の標準（Topografix 策定） | GPS 機器 |
| **GeoJSONSeq** | 1行に1つの Feature を書く GeoJSON（NDJSON と同じ発想） | 大容量データの逐次処理 |
| **Shapefile** | Esri 仕様、バイナリ（複数ファイル） | GIS ソフト |
| **GeoPackage** | SQLite ベース、バイナリ、OGC 標準 | GIS ソフト |

---

## 簡単なGeoJSONファイルの作成

### 実習1: 大学周辺の施設マップ

#### 目標
立命館大学周辺の主要施設を GeoJSON で表現

#### 作成する Feature
1. **大学（キャンパス）**（Point）
2. **最寄り駅**（Point）
3. **通学路**（LineString）
4. **キャンパス敷地**（Polygon）

---

<style scoped>pre { font-size: 15px; }</style>

#### 完全な FeatureCollection
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [135.7245, 35.0330]},
      "properties": {"name": "立命館大学衣笠キャンパス", "category": "university"}
    },
    {
      "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [135.7239, 35.0278]},
      "properties": {"name": "等持院・立命館大学衣笠キャンパス前駅", "category": "railway_station"}
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [[135.7239, 35.0278], [135.7245, 35.0330]]
      },
      "properties": {"name": "通学路", "category": "footway"}
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [[[135.7215, 35.0391], [135.7215, 35.0313], [135.7274, 35.0313], [135.7274, 35.0391], [135.7215, 35.0391]]]
      },
      "properties": {"name": "立命館大学衣笠キャンパス敷地", "category": "university_campus"}
    }
  ]
}
```

---

## 地図上での可視化方法

### geojson.io で表示する

1. [geojson.io](https://geojson.io) を開く
2. 右側のエディタに、作った GeoJSON を貼り付ける（ファイルのドラッグ & ドロップでも可）
3. 地図上に点・線・面が表示される
   - エラーがあると表示されない => カンマや括弧の対応、座標の順番（経度, 緯度）を確認

- 次回以降は **MapLibre GL JS** で自分のウェブページに表示する

---

<div class="assignment">

<style scoped>section { font-size: 24px; }</style>

## 課題: 自分で GeoJSON データを作成し、地図に描画する

以下のお題から1つ選んで GeoJSON を作成してください（迷ったら相談してください）。

| お題 | Point | LineString | Polygon |
|--|--|--|--|
| 物語の舞台 | 作中に登場する場所 | 登場人物の移動ルート | 物語の舞台となる範囲 |
| 歴史上の人物の足跡 | ゆかりの地 | 旅や移動の経路 | 活動した範囲 |
| 推しの地図（スポーツ・音楽・食など） | 好きな店や会場 | 遠征・巡礼のルート | よく行くエリア |

- **Point / LineString / Polygon** をそれぞれ1つ以上含む `FeatureCollection` にすること
- それぞれの Feature の `properties` に `name` と、テーマに合った属性（例: 場面、年代、評価）を1つ以上つけること
- geojson.io などで地図に表示し、正しく描画されることを確認すること

</div>

---

<div class="assignment">

### 提出要項

- **提出物**: 作成した GeoJSON ファイル（`.geojson`）と、地図に表示した画面のスクリーンショット
- **提出期限**: 次回授業開始時（日付は moodle+R で確認）
- **提出方法**: moodle+R 経由
- **評価ポイント**: GeoJSON として正しいか、ジオメトリと属性の適切さ

### 次回の冒頭: テーマ当てクイズ

教員も GeoJSON を1つ作ってきます。お互いに**地図だけ**を見せ合ってテーマを当て、そのあと `properties` を開いて答え合わせをします

</div>


---

<!-- _class: title -->

# ありがとうございました

## 次回もよろしくお願いします

## 次回予告

### 第6回：ウェブ地図の構成要素
- ベースマップとデータレイヤー
- タイルの仕組み（ラスタタイル vs ベクトルタイル）
- MapLibre GL JS の概要とセットアップ

冒頭で課題 GeoJSON のテーマ当てクイズをします。課題の提出をお忘れなく！
