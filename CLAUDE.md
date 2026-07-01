# gitkraken-i18n

GitKraken Desktop の日本語翻訳リポジトリ。英語ファイル (`en/`) を正として、日本語ファイル (`ja/`) を管理する。

## ディレクトリ構成

```
en/
  v12.2.0/strings.json   # 英語原文（バージョンごと）
  v12.2.1/strings.json
ja/
  v12.2.0/strings.json   # 日本語訳（バージョンごと）
  v12.2.1/strings.json
```

各 `strings.json` のトップレベル構造：

```json
{
  "languageOption": { ... },
  "menuStrings": { ... },
  "strings": { "キー": "値", ... }
}
```

翻訳対象は `strings` オブジェクト内のキーのみ。

## 翻訳ルール

- `{0}` `{1}` などのプレースホルダはそのまま残す
- `<i class="...">` などの HTML タグはそのまま残す
- メニュー文言の `&` はアクセスキー。日本語では末尾に `（&O）` 形式で残す
- Git 用語はカタカナ表記を使う：push→プッシュ、pull→プル、fetch→フェッチ、commit→コミット、branch→ブランチ、stash→スタッシュ、rebase→リベース、merge→マージ
- GitFlow・LFS など固有名詞は翻訳しない
- UI 文言として短く自然な日本語にする（体言止め推奨）
- 「連携する」「連携を解除」など既存の言い回しに統一する（`Services-Connect`, `Services-Disconnect` 参照）
- 文末の句点は英語原文に句点があれば付ける

## 新バージョン対応

新バージョンの英語ファイルが追加されたときは `/project:new-version` コマンドを使う。
