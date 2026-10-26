# らくだ🐫のさいと

WordPressから移行した、Ruby/Jekyll製の静的サイトです。

## サイト内容

緩くプログラミングの勉強を始めたアラフォー主婦・らくだ🐫の記録を掲載しています。

- WordPress記事をJekyllへ移行
- WordPress風のヘッダー・サイドバー・フッター
- カテゴリー一覧・カテゴリー別記事一覧
- タグ一覧・タグ別記事一覧
- GitHub Pagesでの公開を予定

## ローカルでの起動

### Git Bash

```bash
rbenv local 3.2.9
bundle install
bundle exec jekyll serve
```

ブラウザで <http://127.0.0.1:4000/> を開きます。

終了するときは `Ctrl+C` を押します。

### ビルドだけ実行

```powershell
bundle exec jekyll build
```

生成されたサイトは `_site/` に作られます。`_site/` はGit管理対象外です。

## ディレクトリ構成

```text
_posts/             記事
pages/              固定ページ
_layouts/           ページレイアウト
assets/css/         CSS
assets/images/      サイト画像
wp-content/uploads/ 記事で使用する画像
categories/         カテゴリー一覧・カテゴリー別ページ
tags/               タグ一覧・タグ別ページ
_config.yml         Jekyll設定
Gemfile             Ruby/Jekyll依存関係
```

## GitHub Pagesで公開する場合

GitHubリポジトリへpushします。

```bash
git remote add origin https://github.com/rakuda-san-desu/rakuda-san-desu.github.io.git
git branch -M main
git push -u origin main
```

GitHubのリポジトリで、次を設定します。

```text
Settings → Pages → Deploy from a branch
Branch: main
Folder: /(root)
```

公開URLは次の形です。

```text
https://rakuda-san-desu.github.io/
```

## 独自ドメイン

GitHub Pagesの設定で、お名前.comのドメインをCustom domainに登録します。
その後、お名前.comのDNSでGitHub PagesのAレコードまたはCNAMEレコードを設定します。
DNS反映後、GitHub Pagesの`Enforce HTTPS`を有効にします。

## 公開前の注意

- `_site/`、`.jekyll-cache/`、`.bundle/`はコミットしない
- WordPressのXMLや`wp-config.php`はコミットしない
- 非公開記事や個人情報を含む画像を公開しない
- 公開前に記事・固定ページ・画像を確認する
