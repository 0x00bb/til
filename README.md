# til
Today I Learned

---

## 2026-04-05 faster-whisper

`faster-whisper` は初回起動時に内部で `huggingface_hub` を使い、Whisperのモデルファイルをダウンロードする。
ダウンロード済みのモデルは `C:\Users\<user>\.cache\huggingface\hub\` にキャッシュされ、2回目以降は完全オフラインで動作する。

会社PCのAppLockerなどで `hf_xet.dll`（高速DL用ライブラリ）がブロックされる場合は、初回のみ以下の環境変数を設定して起動する：

```bat
set HF_HUB_DISABLE_XET=1
python your_script.py
```

これにより通常のHTTPダウンロードにフォールバックする。モデルがキャッシュされれば以降はこの設定不要。

**Whisperモデルのサイズ目安：**
- `tiny` — 約75MB、高速、精度低め
- `base` — 約145MB、バランス型
- `small` — 約460MB、精度高め、CPUでは重い


## 2026-03-22 — プロダクト開発の環境・戦略について整理した

### 開発環境の全体像

```
Claude Desktop  → "何を作るか"を話し合う相手（仕様策定）
Claude Code     → "作る・直す"を実行する相手（実装・デバッグ）
SPEC.md         → 仕様書（人間向け）
CLAUDE.md       → AIへの指示書（Claudeが最初に読むコンテキスト）
TIL.md          → ノウハウ蓄積
```

### 仕様が動的に変わる場合の流れ

```
Claude Desktopで話し合い・合意
    ↓
SPEC.md / CLAUDE.mdを更新
    ↓
Claude Codeに「CLAUDE.mdを読んで実装して」と渡す
    ↓
Claude Codeが自律的に実装・テスト・修正
```

口頭（チャット）で決めてそのままCodeに実装させると後から追えなくなる。**決まったことは必ずSPEC.mdに書いてからCodeに渡す**のが鉄則。

### CLAUDE.mdに書くべき内容

```markdown
## 設計原則
## 既知の問題・ノウハウ（機種別注意事項含む）
## やってはいけないこと
## ビルド手順（C++移行後）
## 社内SDK仕様の要点（機密レベルに応じて）
```

### Claude Desktop再起動問題の現実解

MCPサーバーはClaude Desktop起動時に一度だけ子プロセスとして立ち上がる設計のため、コード変更のたびに再起動が必要。

現実的な対処：
- `demo_test.py` を育てて再起動なしで確認できる範囲を広げる
- ロジック確認はdemo_test.pyで完結させ、再起動の頻度を最小化
- Claude Code導入後は「編集→テスト→確認」を自動化できる

### C++移行後も基本は同じ

```
Python（今）         C++（将来）
demo_test.py     →  scan_test.cpp（単体テスト）
uv run           →  cmake --build → ./scan_test
Claude Desktop   →  Claude Desktop（同じ）
Claude Code      →  Claude Code（より活躍）
```

追加されるのはビルドステップだけ。ドライバー層とMCPプロトコル層を最初から分離しておくとClaude Codeが自動テストしやすい。

### AIにとって扱いやすいSDKドキュメント形式

扱いやすい順：
1. **Markdown** → 構造がそのまま理解に直結
2. **サンプルコード** → 仕様書より雄弁、コメントが丁寧なら最強
3. **HTML** → 構造は読めるがMarkdownより冗長
4. **MCP（対話的）** → CLAUDE.mdと組み合わせる前提なら強い
5. **Word** → テキストとして渡せばMarkdown相当
6. **PDF** → 最も苦手、特にスキャンPDFは情報損失大

理想的なSDKドキュメント構成：
```
Markdownのリファレンス
    + サンプルコード（機種別注意事項付き）
    + OpenAPI仕様書（機械可読）
    + GitHubリポジトリ
```

### SDK公開方法のベストプラクティス（参考：Twilio）

「Contact Us」方式の背景にある「EULAへの同意確認」「利用実態の把握」を満たしつつオープン化する方法：

```
【公開】GitHubにサンプルコード・APIリファレンス・EULAを置く
【同意】developer.epson.comでアカウント登録・EULA同意を記録
【把握】登録時に利用目的を確認・APIキーで利用実態を管理
【将来】MCPサーバーで仕様を対話的に問い合わせ可能に
```

SDKはGitHubで誰でも読めるが、動かすにはアカウント登録が必要、というTwilioモデルが現実的な第一歩。


### 技術メモ

- WIAプロパティは機種によってIDが異なる場合がある→実機確認必須
- MCPレスポンスの上限は1MB（2026年3月時点）
- base64は元データの約1.33倍のサイズになる


- Open API / Swagger

## Windows Claude DesktopのAppDataパス
- ストア版はパスが通常と異なる：
- %APPDATA%\Claude\  ← 通常版
- C:\Users\xxx\AppData\Local\Packages\Claude_pzs8sxrjxfjjc\LocalCache\Roaming\Claude\  ← ストア版

## Claude Desktopの完全終了
- ×ボタンではバックグラウンドで動き続ける。タスクトレイアイコンを右クリック→「Quit」が必要。MCPサーバーはClaude Desktop再起動で自動的に再起動される。
- claude_desktop_config.jsonの配置場所
- 既存の preferences キーを残しつつ mcpServers を追記する必要がある。上書きすると設定が消える。

## Claude Desktopの設定ファイル
- %APPDATA%\Claude\claude_desktop_config.json

## uvのインストール　
- powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

## 1. pipでインストールされているか確認
- pip show uv

## 2. uvのパスを探す
- where.exe uv

## 2026-03-15
- GitHubでTILサイトをセットアップ。 
- 昔、githubでサイト作るの流行ったよね、と作業しながらオジサンとだべれるClaude君好き

### やったこと
- GitHubでリポジトリを作成（README.mdにチェック）
- `_config.yml` を追加してGitHub Pagesを有効化
- GitHub PagesのURLは `https://ユーザー名.github.io/til/` 

### コマンド一覧

#### 初回設定（1回だけ）
- git config --global user.name "Your Name"
- git config --global user.email "your@email.com"

#### リポジトリをcloneする（新しい環境で1回）
- git clone https://github.com/ユーザー名/til.git

#### 毎日の作業
- git pull origin main        # 作業前に必ずやる
#### README.md を編集して追記
- git add README.md
- git commit -m "TIL: 内容を短く英語で"
- git push origin main

## 2026-03-15
- VS2022 の内部バージョンは "18"（フォルダ名が 2022 ではない）
- 新プロジェクト形式は .sln ではなく .slnx
- エントリポイントは _tWinMain → wWinMain に進化（Unicode固定）
- TCHAR / _T("") マクロは過去のもの、今は WCHAR / L"" が主流
- GlobalMemoryStatusEx + WM_TIMER でメモリのリアルタイム監視が作れる
- Python の PATH は今は自動で通る
- watch_build.py でファイル保存 → 自動ビルドの構成2が完成