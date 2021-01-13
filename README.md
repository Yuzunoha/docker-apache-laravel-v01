## セットアップ手順

1. このリポジトリの master ブランチをチェックアウトする

1. app コンテナのユーザ ID をホスト側と合わせるためのファイル .env を作成する

   - docker-compose.yml があるディレクトリで下記のコマンドを実行する

     ```
     # `id -u` の実行結果はホストによって異なる
     echo DOCKER_UID=`id -u` > .env
     ```

1. コンテナをビルドして起動する

   - docker-compose.yml があるディレクトリで下記のコマンドを実行する

     1. 各コンテナをキャッシュを使わずにビルドする

        ```
        docker-compose build --no-cache
        ```

     1. 各コンテナをバックグラウンドで起動する
        ```
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

## コンテナを終了する方法

- docker-compose.yml があるディレクトリで下記のコマンドを実行する

  ```
  docker-compose down
  ```

## 備考

- ホスト側で html 配下のファイルを編集すれば app コンテナに反映される
- composer コマンドや artisan コマンドは app コンテナの bash で実行する

## artisan を使う方法

- 例 1) migration を行う方法

  - app コンテナの bash で /var/www/html/laravelapp に移動して下記のコマンドを実行する

    ```
    docker@efba441bb520:/var/www/html/laravelapp$ php artisan migrate
    ```

- 例 2) HelloController を作成する方法

  - app コンテナの bash で /var/www/html/laravelapp に移動して下記のコマンドを実行する

    ```
    docker@efba441bb520:/var/www/html/laravelapp$ php artisan make:controller HelloController
    ```

## ブラウザで phpMyAdmin を表示する方法

- http://localhost:10481 にアクセスする
