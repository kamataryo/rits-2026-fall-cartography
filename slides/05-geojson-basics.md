---
marp: true
theme: default
class: title
paginate: true
---

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

GeoJSON vs. JSON

- GeoJSON は JSON フォーマットで位置情報を表すもの
- ウェブシステムなどの文脈では「データ交換フォーマット」などとも呼ばれる

---

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

---

### JSON vs. XML

- XML は階層構造や属性を組み合わせた複雑なデータを表現できる。ドキュメント指向(e.g. HTML)
- XML はスキーマに基づき要素・属性・型の正当性を事前検証できる
- 小規模なデータの場合は JSON の方が可読性が高い
- XML は機械判読にパーサー（処理プログラム）が必要。JSON はブラウザがネイティブサポート

---

### JSON vs. YAML

- データ表現力に大きな違いはない
- YAML の方が人間が読みやすいとされている
- YAML もパーサーが必要なので処理に一手間あり

---

### JSON vs. CSV

- CSV は 表形式のデータに最適で、行＝レコード、列＝フィールド
- JSON のような階層構造やネストされたオブジェクトは表現できない
- スプレッドシートへの取り込み/からの書き出しが容易

---

### なぜ多様なデータフォーマットが存在するのか

- データ構造や用途が多様で、単一フォーマットではすべてに最適化できない
- 可読性、処理効率、バリデーションなどの要件がフォーマットごとに異なる
- 既存システムや業界標準との互換性を確保する必要がある

---

## JSON の基本データ型

### プリミティブ型（基本型）

#### 1. 数値型
```json
42
3.14159
-10
```

---

#### 2. 文字列型
```json
"Hello, World!"
"立命館大学"
"2026-10-27"
```
**重要**: 必ずダブルクオーテーション `""` で囲む

---

#### 3. 真偽値型
```json
true
false
```

#### 4. null型
```json
null
```

---

## JSON の配列型

### 配列の基本構文

```json
[要素1, 要素2, 要素3]
```

#### 特徴
- `[]` 角かっこで囲む
- 要素はカンマ `,` で区切る
- 異なる型の要素を混在可能
- 配列の中に配列やオブジェクトも入れられる

---

#### 例
```json
["りんご", "みかん", "バナナ"]
[1, 2, 3, 4, 5]
[true, false, null, 42, "文字列"]
```

---

### 実習: 配列を書いてみよう

あなたの好きな食べ物トップ3を JSON の配列形式で書いてみよう。

**例**: `["ラーメン", "寿司", "カレー"]`


---

## JSON のオブジェクト型

### オブジェクトの基本構文

```text
{
  "キー1": 値1,
  "キー2": 値2,
  "キー3": 値3
}
```

#### 特徴
- `{}` 波かっこで囲み、`"キー": 値` のペアをカンマ `,` で区切る
- キーは必ず文字列（ダブルクオーテーションで囲む）
- 値は任意の JSON データ型（プリミティブ型、配列型、オブジェクト型）

---

#### オブジェクトの例

```json
{
  "name": "田中太郎",
  "age": 20,
  "student": true,
  "university": "立命館大学",
  "hobbies": ["読書", "映画鑑賞", "プログラミング"]
}
```

改行しなくてもよい。（見やすいように）

```json
{ "店名": "喫茶さくら", "業態": "カフェ", "席数": 20 }
```

---

### 実習: オブジェクトを書いてみよう

今日のお昼ご飯を JSON のオブジェクト形式で表現してみよう。
メニュー名と値段をキーとして使ってください。

**例**: `{"メニュー": "親子丼", "値段": 800}`


---

## JSON 記法のルール

#### 1. 文字列は必ずダブルクオーテーション

✅ 正しい
```json
"Hello"
```

❌ 間違い
```text
'Hello'
Hello
```

※ JSON にはコメントも書けない（`// ...` や `/* ... */` は不可）

---

<style scoped>section { font-size: 24px; }</style>

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

#### 3. 改行・インデントは自由
```json
{"name": "太郎", "age": 20}

{
  "name": "太郎",
  "age": 20
}
```

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

## JSON vs. JavaScript

- JavaScript の文法は JSON フォーマットの**スーパーセット**
- JSON は JavaScript の**サブセット**、正しい JavaScript として解釈可能
- ただし、JavaScript のコードが JSON として解釈できるとは限らない

=>
JSON は、JavaScript の自由な表現を「データ交換」に特化して制限したフォーマット
この制限により、「相互運用性」「安全性」「可搬性」が保証されている

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

#### GeoJSON オブジェクトの種類
1. **Geometry**: 幾何学的形状
2. **Feature**: 地物（ジオメトリ + 属性）
3. **FeatureCollection**: 複数の Feature をまとめたもの

#### 共通ルール
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

#### Point（点）
```json
{
  "type": "Point",
  "coordinates": [135.5, 34.7]
}
```

#### LineString（線）
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

---

#### Polygon（面）
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

**注意**: Polygon の座標は三重配列
- 1段目: リングの配列（外周 + 穴）
- 2段目: リング = 座標点の配列。最初と最後の座標は同じ（閉じた形状）
- 3段目: 座標点 `[経度, 緯度]`
- 外周は反時計回り、穴は時計回りに並べる（RFC 7946 の推奨）

---

<style scoped>pre { font-size: 20px; }</style>

### ドーナツ(参考)

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

---

### 複合 Geometry

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

#### GeometryCollection（参考）

種類の違うジオメトリをまとめたもの。`coordinates` ではなく `geometries` を持つ

```json
{
  "type": "GeometryCollection",
  "geometries": [
    { "type": "Point", "coordinates": [135.5, 34.7] },
    { "type": "LineString", "coordinates": [[135.5, 34.7], [135.6, 34.8]] }
  ]
}
```

---

### Feature オブジェクト

#### 基本構造
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

---

#### プロパティの活用
- `name`: 名称
- `description`: 説明
- `category`: カテゴリ
- その他、任意のキーを使ってプロパティを自由に定義可能
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

## 他の位置情報フォーマットの概要

### KML

Point, LineString, Polygon をサポート。
その他、3D モデルやトラック（時間付きの軌跡）なども。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
  <Document>
    <name>サンプルKML</name>
    <Placemark>
      <name>サンプルポイント</name>
      <description>ここに説明文を入れられます</description>
      <Point>
        <coordinates>135.7681,35.0116,10</coordinates>
      </Point>
    </Placemark>
  </Document>
</kml>
```

---

### GPX

点（wpt）、線（trk/trkpt）、ルート（rte/rtept）をサポート

```xml
<?xml version="1.0" encoding="UTF-8"?>
<gpx version="1.1" creator="Sample" xmlns="http://www.topografix.com/GPX/1/1">
  <wpt lat="35.0116" lon="135.7681">
    <ele>10</ele>
    <name>サンプルポイント</name>
    <desc>ここに説明文を入れられます</desc>
  </wpt>
</gpx>
```

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

#### 1. 大学（キャンパス）（Point）
```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [135.7245, 35.0330]
  },
  "properties": {
    "name": "立命館大学衣笠キャンパス",
    "category": "university",
    "description": "京都市北区のキャンパス"
  }
}
```

---

#### 2. 最寄り駅（Point）
```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [135.7239, 35.0278]
  },
  "properties": {
    "name": "等持院・立命館大学衣笠キャンパス前駅",
    "category": "railway_station",
    "line": "京福電鉄北野線"
  }
}
```

---

#### 3. 通学路（LineString）
```json
{
  "type": "Feature",
  "geometry": {
    "type": "LineString",
    "coordinates": [
      [135.7239, 35.0278],
      [135.7241, 35.0295],
      [135.7243, 35.0312],
      [135.7245, 35.0330]
    ]
  },
  "properties": {
    "name": "駅から大学への通学路",
    "category": "footway",
    "distance_m": 600
  }
}
```

---

#### 4. キャンパス敷地（Polygon）
```json
{
  "type": "Feature",
  "geometry": {
    "type": "Polygon",
    "coordinates": [[
      [135.7215, 35.0391],
      [135.7215, 35.0313],
      [135.7274, 35.0313],
      [135.7274, 35.0391],
      [135.7215, 35.0391]
    ]]
  },
  "properties": {
    "name": "立命館大学衣笠キャンパス敷地（簡略化した四角形）",
    "category": "university_campus"
  }
}
```

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
| 最終プロジェクトのテーマ | 自由 | 自由 | 自由 |

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

## 質疑応答

### 本日の内容について
- GeoJSON の構造・記法について
- 座標系・座標順序について
- データ作成の技術的な質問
- 課題に関する質問

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
