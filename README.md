# 日本の市町村役場 所在地データカタログ + 統一データセット
# Japan Municipal Offices — Location Data Catalog & Unified Datasets

日本語 | [English](#english)

---

「県内の市町村役場の所在地・連絡先一覧」を探している人のためのリポジトリです。

- **カタログ**(`catalog.csv`): 47都道府県について、**県公式の一覧がどこにあるか**
  (URL・形式・機械可読性)をまとめたもの
- **統一データセット**(`aomori.csv` / `iwate.csv` など県ごとのCSV/JSON):
  整備済みの県について、同一の8列スキーマに統一した役場所在地データ(CC0)

方針は「**公式にあるならリンクで示す。無ければ作って置いておく**」から始め、
公式一覧が県ごとに形式バラバラ(HTML/PDF/Excel)である以上、リンクだけでは
不便なため、**リンクは保持しつつ全県同一スキーマのデータセット本体も提供する**
方針に拡張しました。

**公式一覧が原本です。** データセットは `verified_at` 時点のスナップショット
([管轄ナビ](https://kankatsunavi.com/)のデータ基盤から生成)であり、
最新・正確な情報は必ず公式をご確認ください。

## なぜ作ったか

多くの県は「県内市町村の役場所在地一覧」を公式に公開していますが、
公開場所は県ごとにバラバラで(HTML表・PDF・Excel)、探すのに手間がかかります。
さらに岩手県・秋田県のように**県がまとめた機械可読の一覧が見当たらない県**もあります
(岩手は集約ページ自体がなく、秋田は要覧PDFがテキスト抽出不能)。
同じものを探している人が使えるよう、カタログと統一データセットを置いておきます。

## catalog.csv の列

| 列 | 説明 |
|---|---|
| `pref_code` | 都道府県コード(2桁) |
| `pref_name` | 都道府県名 |
| `official_list_url` | 県公式の市町村一覧ページのURL(未調査・存在しない場合は空) |
| `format` | HTML / PDF / Excel |
| `machine_readable` | yes/no(機械的にテキスト抽出・パースできるか) |
| `notes` | 補足(一覧の正式名称、パース時の注意など) |
| `verified_at` | 確認日(YYYY-MM-DD) |
| `dataset` | このリポジトリの統一データセットのファイル名(整備済みの県のみ。公式一覧の有無は `official_list_url` / `notes` を参照) |

未調査の県は `notes=未調査` のまま正直に公開し、確認でき次第埋めていきます。

## 統一データセットの列(aomori.csv / iwate.csv など)

| 列 | 説明 |
|---|---|
| `city_code` | 全国地方公共団体コード(5桁。総務省/国土数値情報N03準拠) |
| `name` | 役場名(市=「〇〇市役所」、町村=「〇〇町役場/〇〇村役場」) |
| `postal_code` | 本庁舎の郵便番号 |
| `address` | 本庁舎の住所(県名プレフィックスなし) |
| `phone` | 代表電話 |
| `official_url` | 自治体公式サイトのトップURL |
| `source_url` | 住所・電話を実際に取得した公式ページのURL |
| `verified_at` | 確認日(YYYY-MM-DD) |

同内容のJSON(`aomori.json` / `iwate.json` など)もあります。
並び順は `city_code` 昇順です。

**政令指定都市の注記**: 政令市は**市役所(市全体コード。例: 14100 横浜市役所)と
各区役所(区コード。例: 14101 横浜市鶴見区役所)の両方**を収録しています。
市だけ・区だけを使いたい場合は `city_code` でフィルタしてください
(市全体コードは下2桁が00)。

## 収集方法

いずれも **公式サイト(県・市町村)のみ**を情報源とし、
[管轄ナビ](https://kankatsunavi.com/)のデータ基盤に投入・検証したうえで
エクスポートしています(その意味で全ファイルが `verified_at` 時点のスナップショットです)。

**県公式の一覧がある県**(青森・宮城・福島・埼玉・千葉・東京・神奈川など):

1. 県公式の一覧(`catalog.csv` の `official_list_url`)を原本として所在地・電話を取得
2. 自治体公式サイトのURLは県公式の市町村リンク集から取得し、
   実際にアクセスして現行URLを確認(リダイレクト先への正規化を含む)
3. 全国地方公共団体コードのマスタとの突合、郵便番号・電話の形式チェック

**県公式の機械可読な一覧が無い県**(岩手・秋田):

1. **公式URLの確定**: 各県の「市町村職員給与・定員管理の公表」クロスリンク集
   (県公式ページ)から全自治体の公式ドメインを取得。県公式からの被リンクで公式性を確認。
2. **住所・電話の取得**: 各市町村公式サイトのフッター/庁舎案内ページを決定的にパース
   (正規表現、NFKC正規化)。合併市の総合支所を誤って拾わないよう**本庁舎**を優先。
3. **検算**: 全国地方公共団体コードのマスタと過不足なく一致することを確認。
   郵便番号・電話の形式、住所に自治体名を含むことを機械チェック。

## メンテナー

壺内靖二郎([管轄ナビ](https://kankatsunavi.com/)・[つぼうち税理士事務所](https://ytsubo.com/))が維持しています。
全国の税務署・都道府県税事務所・市区町村役場の管轄データを整備する過程の副産物として
収集したものを、同じデータを探す人のために公開しています。

## ライセンス

**CC0 1.0 Universal(パブリックドメイン提供)**。事実データにつき自由に利用できます。
`LICENSE` を参照。

## 免責

- 収集時点(`verified_at`)の情報です。**最新かつ正確な情報は必ず各自治体の公式サイトで
  ご確認ください**(庁舎移転・電話番号変更・URL変更等がありえます)。
- 本データの利用によって生じたいかなる損害についても、作成者は責任を負いません。

---

# English

A repository for anyone looking for **machine-readable lists of municipal
government office locations (city/town/village halls) in Japan**.

- **Catalog** (`catalog.csv`): for each of the 47 prefectures, **where the
  prefecture's official list is published** (URL, format, machine readability)
- **Unified datasets** (`aomori.csv` / `iwate.csv`, etc. — one CSV/JSON pair
  per covered prefecture): office location data normalized to a single
  8-column schema across all prefectures (CC0)

The original policy — **link to the official list when one exists; build the
data ourselves when it doesn't** — has been extended: since official lists
come in wildly different formats (HTML/PDF/Excel), the links are kept in the
catalog **and** every covered prefecture also gets a dataset in one uniform
schema.

**The official lists are the source of truth.** Each dataset is a snapshot as
of `verified_at`, generated from the data platform behind
[KankatsuNavi](https://kankatsunavi.com/). Always check the official sites for
the latest information.

## Why this exists

Most prefectures publish an official list of municipal office locations, but
the publishing location and format vary widely (HTML tables, PDF, Excel).
Some prefectures — such as Iwate and Akita — publish **no machine-readable
list at all** (Iwate has no aggregated page; Akita's handbook PDF cannot be
text-extracted due to missing ToUnicode maps in embedded fonts). This
repository saves the next person the search.

## Columns in catalog.csv

| Column | Description |
|---|---|
| `pref_code` | Prefecture code (2 digits) |
| `pref_name` | Prefecture name (Japanese) |
| `official_list_url` | URL of the prefecture's official municipal list (empty if unsurveyed or nonexistent) |
| `format` | HTML / PDF / Excel |
| `machine_readable` | yes/no (whether text can be extracted/parsed mechanically) |
| `notes` | Remarks (official title of the list, parsing caveats, etc.; in Japanese) |
| `verified_at` | Date verified (YYYY-MM-DD) |
| `dataset` | Filename of this repository's unified dataset (covered prefectures only; see `official_list_url` / `notes` for whether an official list exists) |

Unsurveyed prefectures are published honestly as `notes=未調査` ("unsurveyed")
and filled in as they are verified.

## Columns in the unified datasets (aomori.csv / iwate.csv, ...)

| Column | Description |
|---|---|
| `city_code` | Japanese local government code (5 digits; MIC / GSI N03 standard) |
| `name` | Office name in Japanese (city hall = 「〇〇市役所」, town/village hall = 「〇〇町役場/〇〇村役場」) |
| `postal_code` | Postal code of the main office building |
| `address` | Address of the main office building (without the prefecture prefix) |
| `phone` | Main phone number |
| `official_url` | Top URL of the municipality's official website |
| `source_url` | The official page the address/phone was actually taken from |
| `verified_at` | Date verified (YYYY-MM-DD) |

JSON equivalents (`aomori.json` / `iwate.json`, etc.) are also provided.
Rows are sorted by `city_code` ascending.

**Note on designated cities** (政令指定都市): each designated city appears
**both as the city hall (city-wide code, e.g. 14100 Yokohama City Hall) and as
its ward offices (ward codes, e.g. 14101 Tsurumi Ward Office)**. Filter by
`city_code` if you need only one level (city-wide codes end in 00).

## Collection method

All information comes **exclusively from official prefectural and municipal
websites**, loaded into and validated on the data platform behind
[KankatsuNavi](https://kankatsunavi.com/) before export (hence every file is a
snapshot as of `verified_at`).

**Prefectures with an official list** (Aomori, Miyagi, Fukushima, Saitama,
Chiba, Tokyo, Kanagawa, ...):

1. Addresses and phone numbers come from the prefecture's official list
   (`official_list_url` in `catalog.csv`) as the source of truth.
2. Municipal website URLs come from the prefecture's official link collection,
   each verified by an actual HTTP request (normalized to the current URL
   after redirects).
3. Validation against the national local government code master, plus
   mechanical checks of postal/phone formats.

**Prefectures without a machine-readable official list** (Iwate, Akita):

1. **Confirming official URLs**: official domains for every municipality were
   taken from each prefecture's official cross-link index (the "municipal
   staff salary disclosure" link collections). Officialness is established by
   the inbound link from the prefectural government's own page.
2. **Extracting address & phone**: deterministic parsing (regex, NFKC
   normalization) of each municipal site's footer / office-guide page,
   preferring the **main office building** so that branch offices of merged
   cities are not picked up by mistake.
3. **Validation**: asserted an exact one-to-one match against the national
   local government code master; mechanical checks of postal/phone formats and
   that the address contains the municipality name.

## Maintainer

Maintained by Yasujiro Tsubouchi ([KankatsuNavi](https://kankatsunavi.com/) /
[Tsubouchi Tax Accountant Office](https://ytsubo.com/), Japan). The data is a
byproduct of building nationwide jurisdiction data for tax offices, prefectural
tax offices, and municipal governments, published here for anyone looking for
the same information.

## License

**CC0 1.0 Universal (public domain dedication)** — this is factual data; use
it freely. See `LICENSE`.

## Disclaimer

- Data reflects the state at collection time (`verified_at`). **Always check
  each municipality's official website for the latest information** (office
  relocations, phone number changes, URL changes, etc.).
- The author accepts no liability for any damage arising from use of this data.
