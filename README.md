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

`composer create-project laravel/laravel sample-app`

## 作成したLaravelアプリの.envファイルを編集する

- laradockで作成したmysql環境に合わせる

/sample-app/.env
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=default
DB_USERNAME=default
DB_PASSWORD=secret
```

## laradockの.envファイルを編集し、コンテナ再起動

- `APP_CODE_PATH_HOST`を作成したLaravelアプリ名に変更する

/laradock/.env

`APP_CODE_PATH_HOST=../sample-app/`

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

# ハンズオン手順

## Hello Worldする

- コントローラーを作成

`php artisan make:controller HelloController --resource`

→`--resource`をつけることでコントローラー内でCRUD特性の機能を一式セットで作成してくれる

- `/routes/api.php`にルート（コントローラーとのマッピング）を追加する

`Route::get('hello', 'HelloController@index');`

- ルート一覧の確認

`php artisan route:list`

- `http://localhost/api/hello`にアクセスして、コントローラーでreturnした文字列が表示されていればOK

## Eloquentモデルとマイグレーションを作成して、実行する

- Eloquentモデルとマイグレーションを作成する

`php artisan make:model Member -m`

→`-m`をつけることでマイグレーションも一緒に作成してくれる

- マイグレーションの実行

 `php artisan migrate`
 
- DB確認して、テストデータ入れておく

## GETリクエストに対してデータを検索（全件）→結果をjsonで返す

- コントローラーを作成する

`php artisan make:controller MemberController --api --model=Member`

→`--resource`も`--api`も挙動は一緒っぽい

→`--model`をつけることで引数にmodelが入ったメソッドを生成してくれる

- ルートを追加する

`Route::resource('member', 'MemberController');`

- コントローラーのindexメソッドで`Member::all()`を返却する

## GETリクエストに対してデータを検索（1件）→結果をjsonで返す

- コントローラーのshowメソッドで`$member`を返却する


# 時間が余ったらコーナー

## カラムを追加してみる

- テーブル更新用のマイグレーションファイルを作成する

`php artisan make:migration add_column_age_to_members_table --table=members`

→既存のテーブルに変更を加えるときは、`--table`オプションでテーブル名を指定する

- `up()`にカラム追加の処理を追加する

```
$table->integer('age');
$table->string('mail');
```

- `down()`にはカラム削除の処理を追加する

```
$table->dropColumn('age', 'mail');
```

## postしてみる

- MemberControllerの`store()`メソッドに処理を追加する

## putしてみる

- MemberControllerの`update()`メソッドに処理を追加する

## deleteしてみる

- MemberControllerの`destroy()`メソッドに処理を追加する

