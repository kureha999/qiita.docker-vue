---
title: Docker環境でVue.jsを触ってみる。
tags:
  - 'Docker'
  - 'Vue.js'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
今回は、Vue.jsの練習を踏まえてDockerで開発環境を作成しようと思い作成しました。


## 環境
* Node.js - v22.9.0
* Vue3

## 事前準備
```
$ mkdir docker_vue
$ cd docker_vue
$ touch Dockerfile
```

###### Dockerfile編集
```Dockerfile:Dockerfile
ベースイメージの指定
FROM node:22.9

Vue CLIをグローバルにインストール
RUN npm install -g @vue/cli

作業ディレクトリの作成
WORKDIR /app

プロジェクトを自動生成（デフォルト設定で）
RUN vue create vue_practise --default

作成されたプロジェクトに移動
WORKDIR /app/vue_practise

依存関係をインストール
RUN npm install

アプリが使用するポートを開放
EXPOSE 8080

アプリを実行するコマンド
CMD ["npm", "run", "serve"]
```

## ビルドと実行
```
$ docker build -t vue_practise .
$ docker run -p 8080:8080 vue_practise
```

<details><summary>補足(docker run)</summary>

```
docker run -v $(pwd)/vue_practise:/app/vue_practise -p 8081:8080 -it vue_practise
```
こちらにすると、ホストとコンテナ間でファイル共有ができて、
開発する際に、ファイルをリアルタイムで変更できる。

</details>

### 動作確認
プロジェクトが動作しているかを確認する。
**http://localhost:8080**
[![Image from Gyazo](https://i.gyazo.com/8998c970aa4b282ef365bb450a35f6d7.png)](https://gyazo.com/8998c970aa4b282ef365bb450a35f6d7)


## プロジェクトファイルをコピー
現在、Dockerfile内で **vue create** コマンドを使ってVue.jsプロジェクトを自動生成したため、プロジェクトはコンテナ内部で作成されています。そのため、ホストの作業ディレクトリには、生成されたプロジェクトファイルが表示されない状態です。
そのためにホストでも表示させるためにコピーをします。
```
$ docker ps
```
> これで出てきたコンテナIDをコピーする。

```
$ docker cp <コンテナID>:/app/vue_practise ./vue_practise
```
> <コンテナID>こちらにIDを打つ

これで表示されるようになりました。
```
vue_practise/
├── node_modules/
├── public/
├── src/
│   ├── assets/
│   ├── components/
│   ├── App.vue
│   └── main.js
├── package.json
├── Dockerfile
└── ...
```



### 最後に
わざわざ、コピーしなくてもDockerfileで書けるのかどうにかしたらできるかと思いますが、わからなかったのでこのやり方でした汗
まだDocker環境も慣れていないので、
もっと簡単な方法は、あるかと思います。その時は、教えていただきたいです。