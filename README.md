# til
Today I Learned

##2026-03-15
- VS2022 の内部バージョンは "18"（フォルダ名が 2022 ではない）
- 新プロジェクト形式は .sln ではなく .slnx
- エントリポイントは _tWinMain → wWinMain に進化（Unicode固定）
- TCHAR / _T("") マクロは過去のもの、今は WCHAR / L"" が主流
- GlobalMemoryStatusEx + WM_TIMER でメモリのリアルタイム監視が作れる
- Python の PATH は今は自動で通る
- watch_build.py でファイル保存 → 自動ビルドの構成2が完成