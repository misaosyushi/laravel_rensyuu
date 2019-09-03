# Laravel環境構築手順

## Laradockをクローンする
- 適当なディレクトリを作って、Laradockリポジトリをクローン

```
mkdir laravel_handson

cd laravel_handson
```

`git clone https://github.com/LaraDock/laradock.git`

## .envファイルを作成する

- /laradock配下の`env-example`をコピー

```
cd laradock
cp env-example .env
```

- .env内の`MYSQL_VERSION`を5.7に書き換える
    - latestのver8だとマイグレーションの実行ができなかったため、5.7とする

## コンテナを起動する

`docker-compose up -d nginx mysql phpmyadmin`

- 立ち上がったか確認

`docker ps`

or

`ctop`

## Laravelアプリを作成する

- workspaceコンテナに入る

`docker-compose exec --user=laradock workspace /bin/bash`

- Laravelをインストール

`composer create-project laravel/laravel sample_app`

## 作成したLaravelアプリの.envファイルを編集する

- laradockで作成したmysql環境に合わせる

/sample_app/.env
```
DB_HOST=mysql # 127.0.0.1 から変更
DB_DATABASE=default # homestead から変更
DB_USERNAME=default # homestead から変更
```

## laradockの.envファイルを編集し、コンテナ再起動

- APP_CODE_PATH_HOSTを作成したLaravelアプリ名に変更する

/laradock/.env

`APP_CODE_PATH_HOST=../sample_app/`

- dockerを再起動する

`docker-compose stop`

`docker-compose up -d nginx mysql phpmyadmin`

## マイグレーションを実行して、DBを確認する

- workspaceに入ってマイグレーションを実行する

`docker-compose exec workspace /bin/bash`

`php artisan migrate`

- VSCodeの人はphpMyAdmin`http://localhost:8080/`のほうが見やすいかも

    - mysqlコンテナ接続して確認でも、やりやすいほうでOK

- PHPStorm/IntelliJのDB接続情報
```
Host: 0.0.0.0
Post: 3306
User: default
Password: secret
Database: default
```
