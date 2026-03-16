本を読書リストに追加するコマンド。

## 入力

$ARGUMENTS

## 手順

1. **引数を解析する**
   - 本のタイトル（必須）を取得する
   - `finished` というキーワードがあればステータスを `finished` に、なければ `reading` にする

2. **本の情報を Web 検索で調べる**
   - WebSearch を使って以下を調査する:
     - ISBN（13桁）
     - 著者名
     - 出版社の公式ページ URL（出版社サイト > Amazon などの書店サイトを優先）
   - 検索クエリ例: `「{タイトル}」 ISBN 出版社`、`"{タイトル}" ISBN publisher`

3. **`content/books/books.json` を読み込む**

4. **新しいエントリを JSON 配列の末尾に追加する**
   - フォーマット（`status: "finished"` の場合）:
     ```json
     {
       "id": "{ISBN}",
       "title": "{タイトル}",
       "author": "{著者}",
       "isbn": "{ISBN}",
       "dateRead": "{今日の日付 YYYY-MM-DD}",
       "url": "{出版社URL}"
     }
     ```
   - フォーマット（`status: "reading"` の場合）:
     ```json
     {
       "id": "{ISBN}",
       "title": "{タイトル}",
       "author": "{著者}",
       "isbn": "{ISBN}",
       "url": "{出版社URL}",
       "status": "reading"
     }
     ```
   - `finished` の場合は `dateRead` に今日の日付を設定し、`status` フィールドは省略する（デフォルトが `finished` のため）
   - `reading` の場合は `dateRead` を省略し、`status: "reading"` を明示する

5. **重複チェック**: 同じ ISBN が既に存在する場合は追加せず、ユーザーに報告する

6. **結果を報告する**: 追加した本の情報（タイトル、著者、ISBN、URL、ステータス）を表示する
