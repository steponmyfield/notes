# Docker

Docker とは、**「コンテナ型仮想化技術」を実現するプロダクト**です。Docker により、実行環境の差異を気にする必要が無くなり、開発を効率的に行えます。

- [【図解】Docker の全体像を理解する -前編-](https://qiita.com/kotaro-dr/items/b1024c7d200a75b992fc)

## Installation

### Linux

#### 1. 必要なパッケージのインストール

```bash
sudo apt-get update
sudo apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common
```

#### 2. Docker の公式 GPG キーを追加

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

#### 3. Docker の`apt`リポジトリを`stable`リポジトリとして設定

```bash
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

#### 4. インストール

```bash
sudo apt-get update
sudo apt-get install docker-ce
```

### Mac

公式サイトから Docker のアカウントを作ってログインし、[DockerHub](https://hub.docker.com/editions/community/docker-ce-desktop-mac)から「Docker Desktop」をダウンロードしてインストールします。

### Windows

[ここ](https://qiita.com/tettekete/items/086ea3bc8a798cae33f5)を参照して下さい。

## Image

コンテナを起動するのに必要な設定ファイルをまとめたもの。

### image の用意

| command | description                      |
| ------- | -------------------------------- |
| `pull`  | `Docker Hub`からダウンロードする |
| `build` | `Dockerfile`からビルドする       |

#### Docker Hub から`pull`

```bash
docker pull [NAME:TAG]
docker pull ubuntu:18.04  # example
```

#### Dockerfile から`build`

```bash
docker build -t [NAME:TAG] [PATH]
docker build -t myubuntu:latest . # example
```

- `-t list`, `--tag list`：名前とタグ（name:tag）
- `-no-cahe`：chache を無効にする

### image の削除・その他の操作

| command  | description    |
| -------- | -------------- |
| `rmi`    | イメージの削除 |
| `images` | イメージの一覧 |

## Container

### 取得から起動まで

| command  | description                             |
| -------- | --------------------------------------- |
| `create` | コンテナをイメージから作成する          |
| `start`  | コンテナを起動する                      |
| `run`    | `pull`-->`create`-->`start`を一括で行う |

- **作成**：`docker create --name [NAME] [IMAGE:TAG]`
  - `--name string`：コンテナに付ける名前
- **起動**：`docker start [IMAGE]`
- **取得・作成・起動を同時に**：`docker run [IMAGE]`
  - `-it` ホストとコンテナの標準入出力を繋げる。`-i`が入力、`-t`が出力。コンテナ内部で作業する時に使う。
  - `-d IMAGE` コンテナ内に入らず、バックグランドでコンテナを動作させる。`-d`を付けずに`it`で入った時、`exit`すると同時にコンテナも終了してしまう
  - `-p [HOST]:[CONTAINER]` コンテナのポートをホスト側でも使えるようにする。
  - `-v [HOST]:[CONTAINER]` ホストとコンテナでファイルを共有する。
  - `-e [ENV VAR]` 環境変数を指定する
  - `--env-file=[ENV FILE]` 環境変数をファイルから指定
  - `--name [name]` コンテナに付ける名前

### 停止から削除まで

| command | description                                    |
| ------- | ---------------------------------------------- |
| `stop`  | コンテナの停止                                 |
| `rm`    | コンテナの削除。停止していないとエラーがでる。 |

- **停止**：`docker stop [コンテナ名]`
- **削除**：`docker rm [コンテナ名]`

### その他のコンテナ操作

| command  | description                                                                      |
| -------- | -------------------------------------------------------------------------------- |
| `ps`     | コンテナの一覧                                                                   |
| `attach` | コンテナのシェルへ接続                                                           |
| `exec`   | コンテナ内で任意のコマンドを実行させる。                                         |
| `commit` | コンテナからイメージを作成。一時保存用。基本は`Dockerfile`をビルドする方が良い。 |

- **一覧**：`docker ps`
  - `-a` 停止中も含む全てのコンテナを表示
- **起動中のコンテナのシェルへ接続**：`docker attach [起動コンテナ名]`
- **コンテナからイメージを作成**：`docker commit [コンテナ名] [イメージ名]:[タグ名]`

## よく使う公式イメージ

### MySQL

デフォルトで「**3306**ポート」でリッスンする。

コンテナ起動時`run`や、`docker-compose`で使える環境変数。

| env                 | description                        |
| ------------------- | ---------------------------------- |
| MYSQL_DATABASE      | 新規作成する DB 名。               |
| MYSQL_USER          | 新規追加する user 名。             |
| MYSQL_PASSWORD      | 上記で追加した user のパスワード。 |
| MYSQL_ROOT_PASSWORD | root のパスワード。                |

#### 例

```bash
docker run --name mysql -e MYSQL_DATABASE=my_db -d -p 3306:3306 mysql
```

## Dockerfile

| command    | desctription                   |
| ---------- | ------------------------------ |
| FROM       | ベースイメージの指定           |
| MAINTAINER | Dockerfile の作成者            |
| RUN        | コマンド実行                   |
| VOLUME     | マウントするボリューム         |
| ADD        | ファイル・ディレクトリの追加   |
| COPY       | ファイルのコピー               |
| CMD        | デーモン実行                   |
| ENTRYPOINT | デーモン実行                   |
| LABEL      | ラベル                         |
| USER       | ユーザー                       |
| EXPOSE     | エクスポートするポート         |
| WORKDIR    | 作業ディレクトリ               |
| ENV        | 環境変数                       |
| ONBUILD    | ビルド完了後に実行するコマンド |

### Commands

#### FROM

```dockerfile
FROM イメージ名：タグ名
```

#### RUN

```dockerfile
RUN apt update && apt install パッケージ名
```

### Example

```Dockerfile
FROM ubuntu:18:04
```

## Best Practices

[Dockerfile のベストプラクティス](http://docs.docker.jp/engine/articles/dockerfile_best-practice.html)

- 全体
  - コンテナはエファラメル（短命）であるべき
  - いつでも停止・破棄可能であるような最小の構築にすること
  - `dockerignore`ファイルを使う
  - 不要なパッケージのインストールを避ける
  - 1 コンテナに 1 プロセス
  - サービスとサービスに依存関係がある場合は、`link`を使う
  - レイヤ数を最小にする
  - 複数行の引数はアルファベット順に
  - キャッシュについて
  - `build`時に`--no-cache`オプション指定でキャッシュを無効にできる
  - `ADD`と`COPY`はファイルに変更があれば、キャッシュが無効になる
  - `RUN apt-get -y update`等では、キャッシュは有効になる
- FROM
  - 公式イメージを用いる
    - `Debian`が推奨
- RUN
  - `RUN apt-get update`と`apt-get install`は常に同じ`RUN`文で連結する
    - `apt-get udpate`のキャッシュ問題のため
- ADD と COPY
  - 一般的なファイルのコピー用途には、`COPY`を使う
  - `ADD`は、ローカルの`tar`ファイルをイメージに自動展開する際などに使う
  - `COPY`は複数ステップで記述し、`RUN`によるパッケージインストールよりも前に記述する
  - ファイル変更による、キャッシュの無効化が予想されるため
