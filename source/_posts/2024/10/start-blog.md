---
title: ブログ開始
date: 2024-10-30 17:10:10
updated: 2024-11-2 14:16:28
tags:
 - Hexo
 - Icarus
 - Git
category: blog
---

学んだ知識のメモ書きとして、技術ブログの立ち上げを思い立つ。

QiitaやZennなどのを既存ツールを検討したものの、自由度や視認性の高さなどに惹かれて静的サイトジェネレーターに決めた。テーマはHexoのicarus。

ホスト先はGitHub Pagesにした。管理が楽だというネットの評判で決めたが、Gitを学び始めて数日の超初心者のため諸々見事にハマり、立ち上げに数日かかった。。。

<br>

---
# 手順
---

やることは大体こんな感じ。

<!-- toc -->

<br>


## 1. Node.jsのインストール
---

homebrewでNode.jsをインストール＆確認。

~~~
$ brew install node
$ node -v
~~~

<br>
<br>

## 2. Gitのインストールと初期設定
---

- Gitインストール(brew)
- Gitユーザ設定＆メール設定
- GitHubアカウント作成
- GitHubでリポジトリを作成

ググればたくさん情報が出てくるので省略。

<br>
<br>

## 3. Hexoをインストールして起動
---

インストール＆確認。

~~~
$ sudo npm install hexo-cli -g
$ hexo -v
~~~
<br>

ローカルにHexoの雛形を作成して、必要なパッケージをインストール。

~~~
$ hexo init myblog
$ cd myblog
$ npm install
~~~
<br>

hexo initコマンドの後ろにファイル名をつければ、ファイルを作成してその中に雛形をデプロイしてくれる。`INFO  Start blogging with Hexo!`と表示されればOK。

準備完了。ローカルで一旦立ち上げてみる。

~~~
$ hexo server
~~~
<br>

`[info] Hexo is running at https://localhost:4000/. Press Ctrl+C to stop.`と表示されるので、localhost:4000にアクセスしてみる。止める時はCtrl+C。

<br>
<br>

## 4. Hexoのテーマ（Icarus）を導入
---

自分は3カラムのデザインにしたいと思い、Hexoのテーマを物色。

色々なデザインテーマが公開されているが、自分がしたいと3カラムのものは意外と少なく、シンプルな感じのIcarusに決定。

Icarus
https://ppoffice.github.io/hexo-theme-icarus/uncategorized/getting-started-with-icarus/

が、このテーマ導入でまずハマる。

<br>

### ハマりポイント①：インストール方法が２種類ある

Icarusのインストール方法はgit cloneとnpm経由の２種類があり、それぞれインストールされるフォルダが違う。

前者はmyblog/themesの下にicarusというフォルダ名でインストールされるが、後者はmyblog/node_modulesの下にhexo-theme-icarus（とhexo-renderer-inferno）がインストールされる。

最初にnpmでインストールした後、インストール先がわからずcloneもしてしまった。

その後にthemesの下のフォルダの方で設定を変更したが、デプロイ後に反映されず、node_modules側を読み込んでいるのだと気づくのに時間がかかった。

<br>

自分はnpm経由を選択。「Install via NPM」のタブからコマンドを実行。

~~~
$ npm install -S hexo-theme-icarus hexo-renderer-inferno
~~~
<br>

その後、myblogのフォルダ内にある`_config.yml`のthemesの項をicarusに変更。

公式では`hexo config theme icarus`コマンドが紹介されているが、コマンドを実行したらymlファイルのレイアウトが変わって気持ち悪かったので、手動で対応。

~~~
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: icarus
~~~
<br>

ここまでで一旦ローカルで立ち上げ直してみて、Icarusがテーマとして反映されているか確認。

~~~
$ hexo generate
$ hexo server
~~~
<br>

localhost:4000を見てみて、テーマが反映されていればOK。

<br>

### ハマりポイント②：_config.icarus.ymlファイルが見当たらない

`hexo generate`は、静的NTMLをpublicフォルダに生成してくれるコマンド。

ローカルでの動作確認だけなら`hexo server`の前に実行する必要はないが、Icarusのデザイン調整を行う`_config.icarus.yml`ファイルは、`hexo generate`実行後に生成される。

このことを知らず、当初`hexo generate`を実行せずに`hexo server`だけ実行してローカルで動かしつつデザイン調整をしようとしたため、肝心のymlファイルが見当たらなくて焦った。

<br>
<br>

## 5. GitHubにプッシュしてPagesで公開
---

エディタで`_config.yml`を以下のように編集。デプロイの項目は`repo:`と`branch:`を追記。

~~~
# URL
url: https://XXXXXX【ユーザ名】.github.io/XXXXXX【リポジトリ名】/

# Deployment
deploy:
  type: git
  repo: git@github.com:XXXXXX.git【GitHubのリポジトリのURL】
  branch: master
~~~
<br>

上のURL情報はデプロイに必要か分からないが、一応入れておく。

これをhexoのコマンドでデプロイするが、Gitへのデプロイヤーが必要なため先に入れておく。

~~~
$ npm install hexo-deployer-git --save
$ hexo deploy -g
~~~
<br>

その後、リポジトリの「Settings」→「Pages」→「Build and deployment」の箇所でBranchを「master」に選択してsave。

しばらくすると、「Pages」に`Your site is live at https://XXXXX`という表示が出るので、アクセスしてみて見れればOK。

<br>

### ハマりポイント③：icarusがsubmoduleとして認識されることがある

最初ビルドした際に、エラーが発生して正常に公開できなかった。

エラーメッセージを見たところ、どうやらicarusをsubmoduleとして認識しているが.gitmoduleファイルに記述が見当たらない、というものらしい（そもそもsubmoduleという概念自体を知らなかったため、ここでもまた理解するのに時間がかかった）。。。

原因を特定したはいいものの、現存のicarusフォルダをsubmoduleする方法もその後のsubmoduleの管理方法を調べてみても、超初心者にはよくわからない。。。

よくよく調べてみると、そもそもPagesはデフォルトでJekyllという自動ビルドツールを使っており、このJekyllがicarusをsubmoduleと認識してしまっているっぽい。

`.nojekyll`という空ファイルをディレクトリ内に置いておくとJekyllの自動ビルドが行われないとのことで、これを試してみたところ無事ビルドされて公開できた。

~~~
$ touch .nojekyll
~~~

<br>
<br>

## 6. 感想
---

主に参考にさせていただいたサイトは以下のとおり。

https://liginc.co.jp/web/programming/server/104594
https://qiita.com/ORCHESTRA_TAPE/items/092d1479ff6dcc7e51c2
https://tech.qookie.jp/posts/hexo-deploy-github-pages-backup-version/

Gitに限らず全般的なリテラシー不足なため、諸々初歩的な点で躓いてしまうが、備忘のため恥を忍んで記録に残す。

＃デザインファイル（hexo-theme-icarus/style/*.styl）の変更が`hexo deploy -g`コマンドでもうまく反映できないのが追加課題。。。

<br>
<br>
