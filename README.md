# Local OCR — Paddle専用版 0.9.1

Paddle系の文字認識・日本語再確認・表復元だけを使うWindows用ローカルOCRです。GPU・Pythonのインストール・初回のモデル取得は不要です。ネット接続なしで動作します。

ZIPを展開し、フォルダー内のLocalOCR.exeを開いてください。exeだけを移動せず、_internalなどの付属フォルダーも一緒に保持してください。

## 読み取りとコピー

1. 「PDF・画像を開く」から原稿を選びます。
2. 「請求書向け（Paddle）」で「このページを読み取る」を押します。
3. 原本上の表を選び、右側で結果を確認します。セルのダブルクリックで修正できます。
4. 「選択した枠をコピー」でExcel・Googleスプレッドシートに貼り付けます。結合・改行を含む形式をコピーします。
5. 必要なら「エクスポート」からExcelなどへ保存します。

「範囲を指定」で原本を囲み、「範囲内を表として認識」を選ぶと、その範囲を表として再解析できます。同じ範囲を複数ページへ適用する場合は「同じ範囲をまとめて読取」を使います。ページは1枚ずつ処理し、途中停止は現在のページを処理した後に反映します。

## 修正と表示

- 選択セルは青く表示されます。見出しの境界をドラッグすると列幅・行高を変えられます。
- 「行・列を編集」「セルを結合」「結合を解除」で表を直せます。
- 原本の認識枠を選んで「認識枠を削除」で消せます。原本画像は変わりません。
- 「元に戻す」「やり直す」で修正を戻せます。
- 範囲読み取りは範囲内の認識結果だけを更新します。
- 自動保存はありません。必要な結果をコピー・保存してから閉じてください。

## 読み取りの限界

文字の誤読・欠落、結合や行列の誤りが残ることがあります。必ず原本と照合してください。RAM 8GB・CPUのみの構成を想定していますが、Ryzen 5 3300U実機では未検証です。表示するPDF画像と一括読み取りは1ページずつ処理します。

## データとライセンス

モデルは同梱し、起動時にローカルのSHA-256で整合性を確認します。モデルが不足・不一致の場合に自動でダウンロードすることはありません。原本・OCR結果はメモリで扱い、外部APIやサーバーは使いません。Pythonのネット接続・名前解決・待受けを拒否し、ONNX Runtimeの利用状況送信を無効にしています。

モデルの出典・ハッシュ・ライセンス原文は_internal/models-paddle、ライブラリの通知は_internal/licensesを参照してください。ソースは「ソース」フォルダーにあります。向き補正もPaddle由来のモデルです。

## 開発用の再ビルド

Windows・Python 3.12を使います。

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install --no-deps -r requirements-lock.txt
.venv\Scripts\python prepare_japanese_models.py
.venv\Scripts\python prepare_tables.py
.venv\Scripts\python prepare_paddle_only_models.py
.venv\Scripts\python collect_licenses.py
.venv\Scripts\python -m PyInstaller --noconfirm --distpath dist-v0.9.1 LocalOCR.spec
.venv\Scripts\python package_release.py --version 0.9.1
```

RapidAIの一部ライブラリはopencv-pythonへの依存を宣言していますが、この配布物はopencv-python-headlessのcv2を使用します。複数のOpenCV配布物を同時に導入しないでください。
