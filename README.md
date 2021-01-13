## セットアップ手順

1. このリポジトリの master ブランチをチェックアウトする

1. app コンテナのユーザ ID をホスト側と合わせるためのファイル .env を作成する

   - docker-compose.yml があるディレクトリで下記のコマンドを実行する

     ```
     # `id -u` の実行結果はホストによって異なる
     echo DOCKER_UID=`id -u` > .env
     ```

1. コンテナのビルドして起動する

   - docker-compose.yml があるディレクトリで下記のコマンドを実行する

   ```
   # 各コンテナをキャッシュを使わずにビルドするコマンド
   docker-compose build --no-cache

   # 各コンテナをバックグラウンドで起動するコマンド
   docker-compose up -d
   ```

1. 起動中の app コンテナの bash を、app コンテナのユーザ"docker"の権限で実行する

   ```
   # appコンテナのユーザ"docker"の権限でappコンテナのbashを実行するコマンド
   docker-compose exec --user docker app bash
   ```

   - 下記のようなプロンプトに切り替わる

     ```
     docker@efba441bb520:/var/www/html$
     ```

1. app コンテナの bash で laravelapp を install する

   1. app コンテナの bash で /var/www/html/laravelapp に移動する

      ```
      docker@efba441bb520:/var/www/html$ cd laravelapp/
      docker@efba441bb520:/var/www/html/laravelapp$
      ```

   1. composer install を実行する

      ```
      docker@efba441bb520:/var/www/html/laravelapp$ composer install
      ```

   1. storage フォルダの制限を緩和する

      ```
      docker@efba441bb520:/var/www/html/laravelapp$ chmod 777 -R storage
      ```

1. laravelapp をブラウザで表示する

   - http://localhost:10480 にアクセスする
   - このセットアップ手順により CakePHP 超入門 Chapter1 の環境構築作業を飛ばせる

## ここまで書いた

---

## 備考

- ホスト側で quelcode-cakephp/html 配下のファイルを編集すれば php コンテナに反映される
  - docker のボリュームマウントという仕組みを使っているため
- composer コマンドや bake コマンドを使うときは前述の php コンテナの bash を通じて行う
  - ユーザ id を一致させているためコンテナで生成したファイルをホスト側で編集可能

## 起動中のコンテナの bash を終了する方法

- コンテナの bash で下記のショートカットキーを入力する

  ```
  ctrl + p + q
  ```

  - コンテナの bash で exit コマンドを打つとコンテナ自体が終了してしまう恐れがあるので非推奨

## migration を行う方法

- php コンテナの bash で /var/www/html/mycakeapp に移動して下記のコマンドを実行する

  ```
  docker@e6e656dc2f0d:/var/www/html/mycakeapp$ ./bin/cake migrations migrate
  ```

  - 同様に bake 等も実行可能

## ブラウザで テキストに記載されている url にアクセスする方法

- 下記のように port を指定し、mycakeapp を省略してアクセスする
  - http://localhost/mycakeapp/hello.html ⇒ http://localhost:10380/hello.html
  - http://localhost/mycakeapp/auction/add ⇒ http://localhost:10380/auction/add

## ブラウザで オークションアプリを表示する方法(課題用のブランチにおいて)

- http://localhost:10380/auction にアクセスする
  - http://localhost:10380/users/add からユーザを作成できる
  - clone 直後の master ブランチには存在しない。課題用のブランチにおいて migration を行う必要がある

## ブラウザで phpMyAdmin を表示する方法

- http://localhost:10381 にアクセスする
  - root 権限で操作可能

## nginx のドキュメントルートを変更する方法

- docker/nginx/default.conf を例えば下記のように編集すると、mylaravelapp のウェブルートディレクトリを nginx のドキュメントルートに設定できる

  ```diff
  server {
  - root  /var/www/html/mycakeapp/webroot;
  + root  /var/www/html/mylaravelapp/public;
    index index.php index.html;
    ...
  ```
