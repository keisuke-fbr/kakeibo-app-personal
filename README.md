# kakeibo-app-personal

個人用の家計簿「**家計簿アプリ_個人**」のフォーム（LINE の LIFF アプリ）です。
LINE のトークで開き、支出の入力と、月ごとの支出の確認ができます。

- 公開 URL（GitHub Pages）: https://keisuke-fbr.github.io/kakeibo-app-personal/
  - 支出入力: `input.html`
  - 支出確認: `summary.html`
- **main に push すると、GitHub Pages に反映されます。**（数分かかることがあります）

## 全体像

```
このフォーム（LIFF）──liff.sendMessages──▶ LINE Bot ──Webhook──▶ GAS ──▶ スプレッドシート（records シート）
                                                                   └─▶ LINE に返信（登録結果／サマリー＋円グラフ）
```

| 役割 | リポジトリ |
|---|---|
| フォーム（このリポジトリ） | `kakeibo-app-personal` |
| GAS（記録・削除・サマリー） | `kakeibo-personal-gas`（private） |

フォームは、GAS を直接呼びません。LINE のメッセージとして送り、Bot が受け取って GAS に渡す仕組みです。

## 画面

### 支出入力（`input.html`）

| 項目 | 形式 | 必須 |
|---|---|---|
| 日付 | 日付（初期値は今日） | はい |
| 内容 | テキスト | はい |
| 種別 | プルダウン（食費 / 本 / サッカー / 服 / コンビニ / 優衣 / その他） | はい |
| 金額（円） | 数値 | はい |
| 支払方法 | プルダウン（楽天Pay / 楽天カード / 三井住友カード / JALカード / 交通系 / 現金 / その他）。初期値は「指定なし」 | いいえ |

送信されるメッセージ（支払方法が「指定なし」のときは4行）:

```
日付
内容
種別
金額
支払方法    ← 選んだときだけ
```

### 支出確認（`summary.html`）

期間（今月 / 月を指定 / 全体）を選ぶと、次のメッセージを送ります。GAS が、合計・種別ごとの金額・前月比と、円グラフを返信します。

```
サマリー
今月        ← または「2026/10」「全体」
```

## 削除

フォームはありません。LINE のトークで `削除` と送り、日付と番号を選びます。（詳しくは、`kakeibo-personal-gas` の README を参照）

## ファイル

| パス | 内容 |
|---|---|
| `input.html` | 支出入力フォーム |
| `summary.html` | 支出確認フォーム |

どちらも、LIFF SDK を読み込む、1ファイル構成です。LIFF ID は、各ファイルの `LIFF_ID` に書かれています（LINE Developers コンソールで発行したものです）。

## 種別・支払方法の候補を変えるとき

- **支払方法**: このリポジトリの `input.html` の `PAYMENT_METHODS` と、GAS（`kakeibo-personal-gas`）の `src/utils.js` の `PAYMENT_METHODS` を、同じ内容にそろえます。GAS 側の候補にない値を送ると、登録されず、エラーが返信されます。
- **種別**: このリポジトリの `input.html` の `CATEGORIES` を変更します。GAS 側は、任意の種別を受け付けます。

## 動作確認

LIFF は LINE アプリ内で動きます。ブラウザ単体では送信できません（`liff.isInClient()` が偽になり、「LINEアプリ内で開いてください。」と表示されます）。
LINE の LIFF URL（LINE Developers コンソールの LIFF タブで確認できます）から開いて確認してください。
