# til
Today I Learned


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