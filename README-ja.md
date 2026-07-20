# gitkraken-i18n
Unofficial GitKraken i18n project  
GitKraken非公式日本語化プロジェクト

このリポジトリは https://github.com/megos/gitkraken-i18n/tree/main からのForkです。

## ディレクトリ構成

翻訳ファイルはGitKrakenのバージョンごとに管理しています。

```
en/v12.2.1/strings.json   # 英語原文
ja/v12.2.1/strings.json   # 日本語訳
```

## Change Language

**言語の切り替えは自己責任でお願いします。言語の切り替えによって[不具合](https://github.com/megos/gitkraken-i18n/issues?q=is%3Aissue+is%3Aopen+label%3A%22known+issue%22)が発生することが報告されています。**

1. このリポジトリの`ja/{バージョン}/strings.json`を、以下の`strings.json`と置き換える
   - Windows: `%LOCALAPPDATA%\gitkraken\app-x.x.x\resources\app.asar.unpacked\src\strings.json`（x.x.xは任意のバージョン）
   - Mac: `/Applications/GitKraken.app/Contents/Resources/app.asar.unpacked/src/strings.json`
1. GitKrakenを再起動
1. 設定を開く  
![Preferences Menu Button](./images/options_button.png)
1. "UI Customization" で言語を選択する 
![UI Customization preferences](./images/ui_customization.png)

## 新バージョンの翻訳

翻訳は [Claude Code](https://claude.com/claude-code) で行います。ビルドツールや依存パッケージのインストールは不要です。

1. GitKrakenから取得した新バージョンの`strings.json`を`en/{バージョン}/strings.json`に配置する
1. `/project:new-version` スラッシュコマンドを実行して`ja/{バージョン}/strings.json`を生成する
1. [Change Language](#change-language) を元に言語を切り替えて翻訳を確かめる

前バージョンの日本語訳を再利用しつつ、新規キー・変更キーのみを翻訳します。キーの並び順と空行の位置は英語版に合わせます。

## 翻訳ルール

プレースホルダやアクセスキーの扱い、Git用語のカタカナ表記、固有名詞の表記ゆれなど、翻訳時のルールはすべて [CLAUDE.md](CLAUDE.md) に記載しています。
