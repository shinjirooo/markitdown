# MarkItDown 使用方法

## 概要

MarkItDownは、さまざまなファイル形式をMarkdown形式に変換するPythonユーティリティです。LLM（大規模言語モデル）やテキスト分析パイプラインで使用するために設計されています。

サポートされているファイル形式：
- PDF
- PowerPoint
- Word
- Excel
- 画像（EXIFメタデータとOCR）
- 音声（EXIFメタデータと音声文字起こし）
- HTML
- テキストベースの形式（CSV、JSON、XML）
- ZIPファイル
- YouTubeのURL
- EPub
- その他

## インストール方法

pip を使用してインストールする場合：

```bash
python3 -m pip install 'markitdown[all]'
```

または、ソースからインストールする場合：

```bash
git clone git@github.com:microsoft/markitdown.git
cd markitdown
python3 -m pip install -e 'packages/markitdown[all]'
```

## 使用方法

### コマンドライン

ファイルをMarkdownに変換して標準出力に出力：

```bash
python3 -m markitdown ファイルパス.pdf > 出力ファイル.md
```

または、`-o`オプションを使用して出力ファイルを指定：

```bash
python3 -m markitdown ファイルパス.pdf -o 出力ファイル.md
```

標準入力からの入力も可能：

```bash
cat ファイルパス.pdf | python3 -m markitdown
```

### オプション依存関係

MarkItDownには、各ファイル形式をサポートするためのオプション依存関係があります。`[all]`を指定すると、すべての依存関係がインストールされますが、個別に指定することも可能です：

```bash
python3 -m pip install 'markitdown[pdf, docx, pptx]'
```

現在利用可能なオプション依存関係：

* `[all]` - すべての依存関係をインストール
* `[pptx]` - PowerPointファイル用の依存関係
* `[docx]` - Wordファイル用の依存関係
* `[xlsx]` - Excelファイル用の依存関係
* `[xls]` - 古いExcelファイル用の依存関係
* `[pdf]` - PDFファイル用の依存関係
* `[outlook]` - Outlookメッセージ用の依存関係
* `[az-doc-intel]` - Azure Document Intelligence用の依存関係
* `[audio-transcription]` - WAVおよびMP3ファイルの音声文字起こし用の依存関係
* `[youtube-transcription]` - YouTubeビデオの文字起こし取得用の依存関係

### プラグイン

MarkItDownは第三者プラグインもサポートしています。プラグインはデフォルトでは無効になっています。インストールされているプラグインを一覧表示するには：

```bash
python3 -m markitdown --list-plugins
```

プラグインを有効にするには：

```bash
python3 -m markitdown --use-plugins ファイルパス.pdf
```

### Azure Document Intelligence

Microsoft Document Intelligenceを使用して変換する場合：

```bash
python3 -m markitdown ファイルパス.pdf -o 出力ファイル.md -d -e "<document_intelligence_endpoint>"
```

### Python API

Pythonでの基本的な使用方法：

```python
from markitdown import MarkItDown

md = MarkItDown(enable_plugins=False) # プラグインを有効にする場合はTrueに設定
result = md.convert("test.xlsx")
print(result.text_content)
```

Document Intelligenceを使用した変換：

```python
from markitdown import MarkItDown

md = MarkItDown(docintel_endpoint="<document_intelligence_endpoint>")
result = md.convert("test.pdf")
print(result.text_content)
```

画像の説明に大規模言語モデルを使用する場合：

```python
from markitdown import MarkItDown
from openai import OpenAI

client = OpenAI()
md = MarkItDown(llm_client=client, llm_model="gpt-4o")
result = md.convert("example.jpg")
print(result.text_content)
```

## 注意事項

- デフォルトでは多くのスクリプトやツールが`/Library/Frameworks/Python.framework/Versions/3.11/bin`にインストールされ、PATHに追加されていない場合があります。
- 音声処理機能を完全に使用するには、ffmpegまたはavconvのインストールが必要です。 