以下では「絶対に失敗しない」を目標に、MarkItDown をすぐ使いこなすための最短ルートを手順形式でまとめます。README の要点だけでなく、つまずきやすい箇所への対策も添えています。

──────────────────
1. 事前準備  
   1.1 Python 3.8 以上をインストール（`python --version` で確認）  
   1.2 可能なら仮想環境を作成  
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows は venv\Scripts\activate
   ```
   1.3 仮想環境の使い方
   仮想環境を使うことで、プロジェクトごとに異なる Python パッケージを管理できます。以下の手順で仮想環境を作成し、使用することができます。
   ```bash
   # 仮想環境の作成
   python -m venv venv
   
   # 仮想環境の有効化
   source venv/bin/activate  # Windows の場合は venv\Scripts\activate
   
   # 仮想環境の無効化
   deactivate
   ```
   仮想環境を有効化すると、プロンプトが `(venv)` となり、インストールしたパッケージはこの環境内に限定されます。

2. インストール（失敗しない型）  
   ― 依存関係を全部まとめて入れるのが一番安全です。  
   ```bash
   pip install --upgrade pip          # 古い pip だと optional install が失敗しやすい
   pip install 'markitdown[all]'      # "絶対に失敗しない" 王道
   ```
   • GPU／LLM 機能など一部オプションはあとからでも追加できます。  
   • macOS の場合、poppler などのネイティブライブラリが要求される PDF 関連で Homebrew が必要なケースがあります（`brew install poppler` で解決）。

3. 動作確認  
   ```bash
   markitdown --version          # 例: 0.1.0
   markitdown --help             # CLI オプション一覧
   ```

4. 最短 CLI 変換フロー  
   4.1 単ファイルを Markdown へ  
   ```bash
   markitdown input.pdf -o output.md
   ```
   4.2 標準入力からのパイプ  
   ```bash
   cat input.pdf | markitdown > output.md
   ```
   4.3 ZIP やフォルダは自動で中身を再帰処理。出力が複数になる場合はファイル名に合わせた Markdown が生成されます。  

   【つまずきポイント対策】  
   • `UnicodeDecodeError` が出た場合 → バイナリファイルをテキストモードで開いていないか確認。最新バージョンではバイナリストリーム必須です。  
   • "対応していない拡張子"と表示 → オプション依存を入れ忘れ。`pip install 'markitdown[xxx]'` で該当機能を追加。  

5. Python API（スクリプトから一発で）  
   ```python
   from markitdown import MarkItDown

   md = MarkItDown(enable_plugins=False)    # 必要なら True
   result = md.convert("sample.docx")
   print(result.text_content)               # Markdown 文字列
   result.save("sample.md")                 # 直接保存も可
   ```
   よくある罠：`convert()` はパス文字列かバイナリストリームを受け取ります。ファイルを開くときは "rb" モード推奨。

6. 画像の説明文を LLM で自動生成したい場合  
   ```python
   from markitdown import MarkItDown
   from openai import OpenAI
   client = OpenAI()

   md = MarkItDown(llm_client=client, llm_model="gpt-4o")
   print(md.convert("example.jpg").text_content)
   ```

7. Microsoft Azure Document Intelligence を利用する場合  
   ```bash
   markitdown myfile.pdf -o out.md -d -e "<https://<resource-name>.cognitiveservices.azure.com/>"
   ```
   • 環境変数 `AZURE_DOCUMENT_INTELLIGENCE_KEY` が設定されていることを確認。  
   • 大量ページや読み取り精度を重視する PDF で効果大。

8. プラグイン機能（必要な人だけ）  
   • インストール済みプラグインの一覧  
   ```bash
   markitdown --list-plugins
   ```  
   • 有効化  
   ```bash
   markitdown --use-plugins input.pdf
   ```
   • GitHub で `#markitdown-plugin` を検索して導入可能。

9. トラブルシューティング早見表  
   | 現象 | 一発解決策 |
   |-----|------------|
   | `ModuleNotFoundError` | ① `pip install 'markitdown[all]'` をやり直し ② 仮想環境がアクティブか確認 |
   | 認証系エラー（Azure, GPT など） | API キー環境変数を設定・再ログイン |
   | 文字化け | 入力ファイルが破損していないか／ソース PDF のフォント埋め込み状態を確認 |
   | 画像の説明が空 | LLM 用オプション `llm_client`, `llm_model` を渡しているか確認 |

10. つねに最新で使うコツ  
   • プロジェクトをフォークし、`packages/markitdown` ディレクトリで開発版を直接インストール  
   ```bash
   git clone https://github.com/microsoft/markitdown.git
   cd markitdown
   pip install -e 'packages/markitdown[all]'
   ```  
   • テスト実行  
   ```bash
   cd packages/markitdown
   pip install hatch
   hatch shell
   hatch test
   ```

──────────────────
これで「絶対に失敗しない」スタートガイドは完了です。基本は `pip install 'markitdown[all]'` → `markitdown <ファイル> -o <出力>`」のワンライナーさえ覚えておけば OK。あとは必要に応じて LLM 連携や Azure Document Intelligence、プラグインを追加していく流れです。
