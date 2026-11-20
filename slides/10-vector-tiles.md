---
marp: true
title: 'ウェブカートグラフィ入門 第10回: ベクトルタイルの活用'
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

## 第10回: ベクトルタイルの活用<br />ベースマップを自分のテーマに合わせる

立命館大学 2026年度 秋セメスター 火曜5限
授業時間: 95分

---

## 本日のアジェンダ

1. **前回の振り返り・課題の確認** (10分)
2. **ベクトルタイルの中身** (20分)
3. **実習1: Maputnik でベースマップを観察する** (20分)
4. **ベースマップは脇役** (10分)
5. **実習2: ベースマップを自分のテーマに合わせる** (25分)
6. **まとめ・課題** (10分)

---

<style scoped>section { font-size: 26px; }</style>

## 前回の振り返り

### 第9回の主要ポイント

- Layer の見た目は **`paint`** と **`layout`** で決まる
- **Expression** で、`properties` やズームに合わせて見た目を変えられる
- **`filter`** で、Layer が描く地物を絞れる
- 種類は色合い、量は大きさや濃さで描き分ける。色分けしたら凡例を置く

### 課題の確認

前回作った地図ページを見せてください

- 何を伝えるために、どう描き分けたか
- 自分のデータは、**ベースマップの上で見やすい**だろうか？

---

## 今日のゴール

前回は、**自分のデータ**の見た目を変えた

今日は、**ベースマップ**の見た目を変える

1. ベクトルタイルの中身を知る
2. ベースマップの Layer を観察する
3. 自分のテーマに合わせて、ベースマップを調整する

=> ラスタタイルは画像なので変えられない。ベクトルタイルだからできる（第7回）

---

## なぜタイルにするのか

自分の GeoJSON は、数十〜数百件。まるごと読み込んでも問題ない

では、**日本中の道路や建物**を GeoJSON で配ったら？

- ファイルが大きすぎて、ダウンロードが終わらない
- ブラウザのメモリが足りなくなる

=> ラスタタイルと同じ発想で、**データを**タイルに分けて、見えている分だけ送る

```text
https://.../{z}/{x}/{y}.pbf
```

---

## PBF: 小さくまとめる形式

- GeoJSON は**テキスト**。数字も座標も文字として書くので、大きくなる
- ベクトルタイルは **PBF**（Protocol Buffers）という**バイナリ形式**で、小さくまとめる
- 人がそのまま読むことはできない

### サイズの比較

地理院のベクトルタイル1枚（ズーム 14、東京付近）を GeoJSON に直すと

| 形式 | サイズ |
|--|--|
| GeoJSON | 1.7 MB |
| PBF | 84 KB（約 1/21） |

---

<style scoped>section { font-size: 26px; }</style>

## ベクトルタイルの中身: ソースレイヤー

<div class="cols">
<div>

1枚のタイルの中で、データは種類ごとに分かれている
この区分けを**ソースレイヤー**という

OpenFreeMap の場合（一部）

| ソースレイヤー | 中身 |
|--|--|
| `water` | 海、湖、川 |
| `transportation` | 道路、鉄道 |
| `building` | 建物 |
| `place` | 地名 |
| `poi` | 店や施設 |

</div>
<svg width="460" height="380" viewBox="0 0 460 380" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="1枚のベクトルタイルの中に、water、transportation、building、place の4つのソースレイヤーが層になって入っている図">
  <polygon points="20,360 200,360 250,300 70,300" fill="#fff" stroke="#999" stroke-width="1.5"/>
  <polygon points="20,360 90,360 150,300 70,300" fill="#bcdcf5"/>
  <polygon points="20,270 200,270 250,210 70,210" fill="#fff" fill-opacity="0.8" stroke="#999" stroke-width="1.5"/>
  <line x1="35" y1="252" x2="235" y2="232" stroke="#e0a030" stroke-width="5"/>
  <line x1="120" y1="270" x2="170" y2="210" stroke="#e0a030" stroke-width="5"/>
  <polygon points="20,180 200,180 250,120 70,120" fill="#fff" fill-opacity="0.8" stroke="#999" stroke-width="1.5"/>
  <polygon points="150,172 180,172 192,157 162,157" fill="#c9c2b8" stroke="#8a8378" stroke-width="1"/>
  <polygon points="175,146 200,146 210,133 185,133" fill="#c9c2b8" stroke="#8a8378" stroke-width="1"/>
  <polygon points="100,145 122,145 131,133 109,133" fill="#c9c2b8" stroke="#8a8378" stroke-width="1"/>
  <polygon points="20,90 200,90 250,30 70,30" fill="#fff" fill-opacity="0.8" stroke="#999" stroke-width="1.5"/>
  <text x="135" y="66" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">北区</text>
  <text x="275" y="66" font-size="18" font-weight="bold" font-family="monospace" fill="#7a5d00">place</text>
  <text x="275" y="156" font-size="18" font-weight="bold" font-family="monospace" fill="#7a5d00">building</text>
  <text x="275" y="246" font-size="18" font-weight="bold" font-family="monospace" fill="#7a5d00">transportation</text>
  <text x="275" y="336" font-size="18" font-weight="bold" font-family="monospace" fill="#7a5d00">water</text>
</svg>
</div>

---

<style scoped>section { font-size: 24px; }</style>

## ソースレイヤーと Layer

名前は似ているが、別のもの

- **ソースレイヤー**: タイルの**データ**の区分け（タイルを作った人が決める）
- **Layer**: **描き方**の指定（地図を作る人が決める。第8回）

```js
{
  id: 'building-fill',
  type: 'fill',
  source: 'openmaptiles',
  'source-layer': 'building',   // タイルの中の、どの区分けを描くか
  paint: { 'fill-color': '#d9d0c9' },
}
```

- GeoJSON の Source には区分けがないので、`source-layer` は書かなかった
- 1つのソースレイヤーから、複数の Layer を作れる
  （例: `transportation` から、高速道路・一般道・鉄道を別々の Layer で描く）

---

## 観察: 自分の地図に届くタイル

1. 自分の地図ページを開く
   （ラスタのベースマップを使っている場合は、AI に頼んで OpenFreeMap に替える）
2. 開発者ツールの**ネットワーク**タブを開き、地図を動かす（第7回）

### 見るポイント

- `.pbf` で終わるファイルはある？ パスに Z/X/Y は並んでいる？
- 1枚のサイズはどのくらい？
- クリックして中身を見ると、どう見える？

---

## Maputnik

- ベクトルタイルの**スタイルを編集する**ための、ブラウザで動くエディタ
- MapLibre のコミュニティが開発しているオープンソースのツール
- Layer を1つずつ選んで、色や太さを**画面で確かめながら**変えられる
- 編集した結果は、**style**（JSON）として保存できる

https://maplibre.org/maputnik/

---

<style scoped>section { font-size: 26px; }</style>

## 実習1: Maputnik でベースマップを観察する（20分）

### 準備

1. [Maputnik](https://maplibre.org/maputnik/) を開く
2. 上のメニューの **Open** から、次の URL を読み込む

```text
https://tiles.openfreemap.org/styles/liberty
```

3. 自分のテーマの場所まで地図を動かす

### 観察する

- 左の一覧に、Layer はいくつある？ 名前から中身を想像できる？
- Layer を1つ選ぶ。`type`、ソースレイヤー、`filter`、`paint` は何が書かれている？
- 表示を **Inspect** に切り替えて、地図をクリックする。どんな `properties` が入っている？

---

## 実習1: 触ってみる

Layer を選んで、右の欄で値を変える

- 建物の色を変える
- 水の色を変える
- 道路を太くする・細くする
- 店や施設のラベルを消す（Layer を非表示にする）

### 見るポイント

- 第9回の `paint` や `filter`、Expression と**同じ書き方**が使われている
- 道路の Layer は、なぜいくつもあるのだろう？

---

## ベースマップは脇役

<div class="cols">
<div>

- 主役は**自分のデータ**（オーバーレイ）
- ベースマップは、場所の**手がかり**になれば十分
- 既製のベースマップは、何にでも使えるように**情報が多い**

=> 足すより、**引く**

</div>
<svg width="500" height="250" viewBox="0 0 500 250" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="左は色や要素の多いベースマップに自分の点が埋もれている地図。右はベースマップを淡くして要素を減らし、自分の点が目立っている地図">
  <defs><marker id="bm-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#666"/></marker></defs>
  <text x="105" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">そのまま</text>
  <rect x="1" y="30" width="208" height="200" rx="8" fill="#f2e9d8" stroke="#666" stroke-width="2"/>
  <polygon points="2,31 70,31 40,90 2,80" fill="#a8d59a"/>
  <polygon points="150,170 208,160 208,229 130,229" fill="#8fc7ee"/>
  <line x1="1" y1="150" x2="209" y2="120" stroke="#f0a020" stroke-width="7"/>
  <line x1="80" y1="30" x2="100" y2="230" stroke="#f6d04d" stroke-width="6"/>
  <line x1="1" y1="60" x2="209" y2="85" stroke="#fff" stroke-width="4"/>
  <line x1="150" y1="30" x2="140" y2="230" stroke="#fff" stroke-width="4"/>
  <circle cx="30" cy="110" r="5" fill="#8a4fbf"/>
  <circle cx="120" cy="50" r="5" fill="#d9822b"/>
  <circle cx="180" cy="105" r="5" fill="#2f8f8f"/>
  <circle cx="60" cy="200" r="5" fill="#8a4fbf"/>
  <circle cx="115" cy="170" r="5" fill="#d9822b"/>
  <circle cx="45" cy="70" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="125" cy="110" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="165" cy="140" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <line x1="222" y1="130" x2="275" y2="130" stroke="#666" stroke-width="2" marker-end="url(#bm-arrow)"/>
  <text x="395" y="18" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">調整したあと</text>
  <rect x="291" y="30" width="208" height="200" rx="8" fill="#f7f7f7" stroke="#666" stroke-width="2"/>
  <polygon points="292,31 360,31 330,90 292,80" fill="#eaefe8"/>
  <polygon points="440,170 498,160 498,229 420,229" fill="#dde6ec"/>
  <line x1="291" y1="150" x2="499" y2="120" stroke="#e2e2e2" stroke-width="7"/>
  <line x1="370" y1="30" x2="390" y2="230" stroke="#e2e2e2" stroke-width="6"/>
  <line x1="291" y1="60" x2="499" y2="85" stroke="#fff" stroke-width="4"/>
  <line x1="440" y1="30" x2="430" y2="230" stroke="#fff" stroke-width="4"/>
  <circle cx="335" cy="70" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="415" cy="110" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
  <circle cx="455" cy="140" r="7" fill="#e5533d" stroke="#fff" stroke-width="2"/>
</svg>
</div>

---

## ベースマップの調整の例

| やること | ねらい |
|--|--|
| 店や施設のラベルを消す | 自分のデータと紛れないようにする |
| 全体の色を淡くする | 自分のデータの色を目立たせる |
| テーマに関係のある地物だけ色を残す | 文脈を伝える（川の地図なら水を濃く） |
| 道路や建物を減らす | 広い範囲を見せる地図をすっきりさせる |

### 考えてみよう

自分のテーマでは、ベースマップの何を残して、何を引く？

---

<style scoped>section { font-size: 26px; }</style>

## 調整の進め方は3つ

1. **既製のスタイルから選ぶ**
   OpenFreeMap には `liberty` のほかに、淡色の `positron` などがある
   ```text
   https://tiles.openfreemap.org/styles/positron
   ```
2. **Maputnik で編集して、style を保存する**
   画面で確かめながら細かく変えられる
3. **AI に頼んで、コードで変える**
   「ベースマップの店や施設のラベルを非表示にしてください」

近いものを 1 で選んでから、2 か 3 で仕上げるのが楽

---

<style scoped>section { font-size: 26px; }</style>

## 実習2: ベースマップを自分のテーマに合わせる（25分）

### ゴール

調整したベースマップの上に、前回描き分けた自分のデータを表示する

### Maputnik で進める場合

1. Maputnik でベースマップを調整する
2. 上のメニューの **Save** から、style をダウンロードする
3. ファイル名を `style.json` にして、HTML と**同じフォルダ**に置く
4. AI に頼む

```text
この地図のベースマップを、同じフォルダにある style.json に替えてください。
data.geojson の Layer はそのまま残してください。
```

---

<style scoped>section { font-size: 24px; }</style>

## 自分のデータを、ラベルの下に入れる

Layer は、後から足したものほど上に重なる（第8回）
=> 自分のポリゴンが、ベースマップの**地名を隠してしまう**

<div class="cols">
<div>

`addLayer` の2つ目に Layer の `id` を書くと、その Layer の**下**に差し込める

```js
map.addLayer(
  { id: 'my-area', type: 'fill', ... },
  'label_city'  // この Layer の下に入れる
)
```

AI には「自分のポリゴンを、ベースマップの地名ラベルより下に表示して」と頼む

</div>
<svg width="420" height="290" viewBox="0 0 420 290" xmlns="http://www.w3.org/2000/svg" font-family="sans-serif" role="img" aria-label="下からベースマップの道路や建物、自分のポリゴン、ベースマップの地名ラベルの順に重なっている図">
  <polygon points="20,270 200,270 250,210 70,210" fill="#f3f3f3" stroke="#666" stroke-width="2"/>
  <line x1="35" y1="252" x2="235" y2="232" stroke="#ccc" stroke-width="5"/>
  <line x1="120" y1="270" x2="170" y2="210" stroke="#ccc" stroke-width="5"/>
  <polygon points="20,180 200,180 250,120 70,120" fill="#fff" fill-opacity="0.6" stroke="#c99a00" stroke-width="2"/>
  <polygon points="75,168 165,168 200,132 110,132" fill="#f7c9c0" stroke="#e5533d" stroke-width="2" stroke-linejoin="round"/>
  <polygon points="20,90 200,90 250,30 70,30" fill="#fff" fill-opacity="0.6" stroke="#666" stroke-width="2" stroke-dasharray="6 4"/>
  <text x="135" y="66" font-size="16" font-weight="bold" text-anchor="middle" fill="#333">北区</text>
  <text x="270" y="58" font-size="16" font-weight="bold" fill="#333">地名ラベル</text>
  <text x="270" y="79" font-size="13" fill="#666">ベースマップ</text>
  <text x="270" y="148" font-size="16" font-weight="bold" fill="#7a5d00">自分のポリゴン</text>
  <text x="270" y="169" font-size="13" fill="#7a5d00">オーバーレイ</text>
  <text x="270" y="238" font-size="16" font-weight="bold" fill="#333">道路・建物・水</text>
  <text x="270" y="259" font-size="13" fill="#666">ベースマップ</text>
</svg>
</div>

---

## 生成されたコードと style.json を読む

- `style:` には何が書かれている？（URL / `./style.json`）
- `style.json` を開く。`sources` と `layers` は見つかる？（第8回）
- `layers` の中から、自分が色を変えた Layer を探す。`paint` はどうなっている？
- `source-layer` には何が書かれている？
- 自分のデータの `addLayer` に、2つ目の `id` は書かれている？

---

<style scoped>section { font-size: 26px; }</style>

## うまくいかないとき

| 症状 | 確かめること |
|--|--|
| 地図が真っ白になる | `style.json` の場所と名前。`http://localhost...` で開いているか（第8回） |
| 自分のデータが消えた | ベースマップを替えたあとも `addSource` / `addLayer` が残っているか |
| 自分のデータが見えにくくなった | ベースマップの色に合わせて、データの色を選び直す（第9回） |
| 文字やアイコンが出ない | Console のエラーを AI に貼る |
| 差し込む先の Layer が見つからない | `id` はスタイルごとに違う。Maputnik の一覧で確かめる |

---

## スタイルを変えても、出典表記は必要

- 見た目を変えても、**データは借りているもの**
- OpenFreeMap のベクトルタイル
  => `OpenFreeMap © OpenMapTiles Data from OpenStreetMap`（第7回）
- 出典表記は Source に入っているので、`style.json` に替えてもそのまま表示される
- 表示されているかを、**自分の目で確かめる**

---

## 自分のデータをベクトルタイルにするには

今日は、**誰かが作ったベクトルタイル**の見た目を変えた

自分の GeoJSON が大きくなって地図が重いときは、自分でベクトルタイルを作れる

- [**tippecanoe**](https://github.com/felt/tippecanoe): GeoJSON からベクトルタイルを作るツール
- [**PMTiles**](https://docs.protomaps.com/pmtiles/): たくさんのタイルを1つのファイルにまとめる形式

数百件までの GeoJSON なら、そのままで十分。プロジェクトで必要になったら相談してください

---

## まとめ

- ベクトルタイルは、**データ**をタイルに分けて、**PBF** で小さくまとめたもの
- タイルの中は**ソースレイヤー**に分かれていて、Layer は `source-layer` で描くものを選ぶ
- ベースマップも Layer の集まり。**Maputnik** で観察・編集できる
- ベースマップは**脇役**。自分のデータが伝わるように、**引く**
- 調整した style は `style.json` として保存し、自分の地図で使える

---

<div class="assignment">

## 課題: テーマに合わせてベースマップを調整する

### 課題内容

ベクトルタイルのベースマップを、仮のテーマに合わせて調整してください。

- ベースマップの見た目を、3か所以上変えること（色を変える、非表示にする、など）
- 前回描き分けた自分のデータが、その上に表示されること
- 出典表記と凡例が表示されていること

</div>

---

<style scoped>section { font-size: 26px; }</style>

<div class="assignment">

### 提出要項

- **提出物**:
  - 作ったファイル一式（zip にまとめる。GitHub リポジトリの URL でも可）
  - 調整する前と後の、画面のスクリーンショット
  - メモ: ベースマップの何を、なぜ変えたか / AI に出した主な指示 / 生成されたコードや `style.json` について確認したこと・直したこと
- **提出期限**: 次回授業開始時
- **提出方法**: moodle+R 経由
- **評価ポイント**: 調整がテーマに合っているか、自分のデータの見やすさ、出典表記、生成されたコードを自分で確かめられているか

</div>

---

## 次回までに

次回から、応用プロジェクトに入ります

- これまでに作ったファイル（HTML、GeoJSON、`style.json`）を、1つのフォルダにまとめておく
- **GitHub** にログインできるかを確かめておく（第1回の前に登録したアカウント）
- 仮のテーマを、もう一度見直しておく

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

**第11回: 応用プロジェクト（1）地図プロジェクトの企画**
