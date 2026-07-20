# 輪廻転生ランダムリセマラゲーム 公開用フォルダ

このフォルダをそのまま静的ホスティングにアップロードすれば公開できます。

公開URL: https://tenma2066-tech.github.io/rinne-tensei-game/

## 中身

- `index.html`: ゲーム本体。検索向けの title / description / OGP / JSON-LD を追加済み。
- `speedtest/`: インターネット速度計測アプリ（下り・上り・Ping・ジッター・バッファブロート計測、回線評価つき）。
  - 公開URL: https://tenma2066-tech.github.io/rinne-tensei-game/speedtest/
  - 速度実測に speed.cloudflare.com、IP情報取得に Cloudflare / ipwho.is / ipapi.co を利用（無料枠のため、アクセスが増えた場合は自前の計測エンドポイント設置を検討）。
- `robots.txt`: 検索エンジンのクロールを許可。
- `sitemap.xml`: 公開URLを検索エンジンへ伝えるサイトマップ。

## 公開方法

一番簡単なのは GitHub Pages か Netlify Drop です。

GitHub Pages:
1. GitHubで新しい公開リポジトリを作る。
2. このフォルダ内の `index.html` と `robots.txt` をアップロードする。
3. リポジトリの Settings > Pages で、公開元を `main` ブランチの root にする。
4. 表示された公開URLを開いて動作確認する。

Netlify Drop:
1. `rinne-tensei-game` フォルダを Netlify Drop にドラッグ&ドロップする。
2. 発行されたURLを開いて動作確認する。

## 検索に出すために必要なこと

- 公開URLをSNS、ブログ、プロフィール、GitHub READMEなどからリンクする。
- Google Search Console に公開URLを登録してインデックス登録をリクエストする。
- 検索結果に出るまで数日から数週間かかることがあります。

ローカルファイルのままでは他人は遊べず、検索エンジンにも登録されません。必ず公開URLが必要です。
