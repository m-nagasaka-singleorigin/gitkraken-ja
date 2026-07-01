# 新バージョンの日本語ファイルを作成する

引数として新バージョン番号を受け取る（例: `/project:new-version v12.3.0`）。
引数が省略された場合は、`en/` 配下のディレクトリ一覧と `ja/` 配下のディレクトリ一覧を比較して未翻訳バージョンを特定し、ユーザーに確認する。

## 手順

### 1. バージョンの特定

```bash
ls en/ && ls ja/
```

`en/` にあって `ja/` にない最新バージョンが対象。`NEW_VER` をそのバージョン、`PREV_VER` をひとつ前のバージョンとする。

### 2. 差分の確認

```bash
diff en/$PREV_VER/strings.json en/$NEW_VER/strings.json
```

差分から以下を整理する：
- **新規追加キー**（`>` 行）
- **変更されたキー**（`<` → `>` の対）
- **削除されたキー**（`<` 行のみ）

### 3. 翻訳の作成

以下の Python スクリプトを実行して `ja/$NEW_VER/strings.json` を生成する。

スクリプトの構成：

```python
import json, re

# ---- ヘルパー：空行のあとにくるキーを抽出 ----
def extract_blank_line_after_keys(filepath):
    with open(filepath, encoding='utf-8') as f:
        lines = f.readlines()
    result = set()
    for i, line in enumerate(lines):
        if line.strip() == '' and i + 1 < len(lines):
            for j in range(i - 1, -1, -1):
                stripped = lines[j].strip()
                if stripped.startswith('"'):
                    m = re.match(r'"([^"]+)"', stripped)
                    if m:
                        result.add(m.group(1))
                    break
    return result

# ---- ファイル読み込み ----
with open(f'en/{NEW_VER}/strings.json') as f:
    en_new = json.load(f)
with open(f'ja/{PREV_VER}/strings.json') as f:
    ja_prev = json.load(f)
with open(f'ja/{PREV_VER}/strings.json', encoding='utf-8') as f:
    ja_prev_text = f.read()

en_new_strings = en_new['strings']
ja_prev_strings = ja_prev['strings']

# ---- 空行位置の引き継ぎ ----
ja_blanks = extract_blank_line_after_keys(f'ja/{PREV_VER}/strings.json')
en_blanks = extract_blank_line_after_keys(f'en/{NEW_VER}/strings.json')
combined_blanks = ja_blanks | (en_blanks - set(ja_prev_strings.keys()))

# ---- 翻訳マップ（新規・変更キーを記載） ----
new_translations = {
    # 新規キー: 日本語訳
    # 例: 'Hoge-Fuga': 'ほげふが',
}
updated_translations = {
    # 英語の値が変わったキー: 更新後の日本語訳
}

# ---- strings ビルド ----
ja_new_strings = {}
for key in en_new_strings:
    if key in updated_translations:
        ja_new_strings[key] = updated_translations[key]
    elif key in new_translations:
        ja_new_strings[key] = new_translations[key]
    elif key in ja_prev_strings:
        ja_new_strings[key] = ja_prev_strings[key]
    else:
        ja_new_strings[key] = en_new_strings[key]  # フォールバック（英語のまま）

# ---- 空行付きで出力 ----
strings_start = ja_prev_text.index('"strings"')
prefix = ja_prev_text[:strings_start]

lines = []
keys = list(ja_new_strings.keys())
for i, key in enumerate(keys):
    value_json = json.dumps(ja_new_strings[key], ensure_ascii=False)
    comma = '' if i == len(keys) - 1 else ','
    lines.append(f'        {json.dumps(key, ensure_ascii=False)}: {value_json}{comma}')
    if key in combined_blanks and i < len(keys) - 1:
        lines.append('')

import os
os.makedirs(f'ja/{NEW_VER}', exist_ok=True)
output = prefix + '"strings": {\n' + '\n'.join(lines) + '\n    }\n}'
with open(f'ja/{NEW_VER}/strings.json', 'w', encoding='utf-8') as f:
    f.write(output)
```

### 4. 翻訳内容の決定（重要）

新規・変更キーの日本語訳を決める際の手順：

1. **既存の類似キーを参照する**
   ```python
   # 同じプレフィックス（例: Services-）のキーを ja/$PREV_VER で確認
   for k, v in ja_prev_strings.items():
       if k.startswith('Services-'):
           print(k, '->', v)
   ```

2. **翻訳ルール**（CLAUDE.md 参照）に従う

3. **変更キーの注意点**：英語の値が数値や文言だけ変わった場合（AI クレジット数など）は、数値部分のみ更新する

### 5. 検証

```python
# JSON として正常にパースできるか確認
with open(f'ja/{NEW_VER}/strings.json') as f:
    parsed = json.load(f)

en_count = len(en_new_strings)
ja_count = len(parsed['strings'])
assert en_count == ja_count, f'キー数不一致: en={en_count}, ja={ja_count}'

# 英語のまま残ったキーを確認（フォールバックになっていないか）
fallbacks = [k for k in parsed['strings'] if parsed['strings'][k] == en_new_strings[k] and k in new_translations]
if fallbacks:
    print('WARNING: 以下のキーが英語のままです:', fallbacks)

print(f'OK: {ja_count} キー, JSON パース成功')
```

### 6. 空行の正確性を確認

```python
ja_blanks_new = extract_blank_line_after_keys(f'ja/{NEW_VER}/strings.json')
en_keys_new = set(en_new_strings.keys())
ja_blanks_prev = extract_blank_line_after_keys(f'ja/{PREV_VER}/strings.json')

should_have = ja_blanks_prev & en_keys_new
missing = should_have - ja_blanks_new
print('Missing blank lines after:', missing or 'なし（正常）')
```

## 完了確認チェックリスト

- [ ] `en/$NEW_VER` と `ja/$NEW_VER` のキー数が一致
- [ ] 新規キーすべてに日本語訳が入っている（英語フォールバックなし）
- [ ] 変更キーの日本語訳が更新されている
- [ ] 削除キーが `ja/$NEW_VER` に含まれていない
- [ ] JSON として正常にパースできる
- [ ] 空行の配置が `ja/$PREV_VER` と同等（新規キー分の増減は許容）
