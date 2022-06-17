# 概要
このプロジェクトはREST APIの利用方法を簡単に紹介するためのプロジェクトです。

# ハンズオン

## お願い

- もし手順にあやまりがあればぜひ教えて下さい!
- 各コマンド実施時に表示されるメッセージはよく読みましょう!
- 問題が起きていることに気づかずどんどんコマンド実行して気づいたらめちゃくちゃに・・・というのはあるあるです。
- エラーで困った際にはSlackなどでスクショや画面録画などをつかいつつ説明いただけると解決までの時間が早くなります!

## 準備

- GitHubのアカウントを準備しましょう
- ユーザー名が必要です
- 個人アクセストークン（Personal Access Token）が必要です
  - Scopeのチェックは何もつけなくても大丈夫です
- Terminal（Git for WindowsなどでもOK）を使います
- [Postman](https://www.postman.com/)のダウンロードをしておきましょう

## GETリクエスト

ターミナルからGitHubに下記をリクエストしてみる。  
```bash
% curl https://api.github.com/zen
Half measures are as bad as nothing at all.
```

レスポンスにはGitHubのデザインフィロソフィーが表示されること。  
※レスポンスの値は定期的に更新される。  

次に下記リクエストでとあるユーザーのプロフィール情報を取得してみる。  
```bash
% curl https://api.github.com/users/defunkt
{
  "login": "defunkt",
  "id": 2,
  "node_id": "MDQ6VXNlcjI=",
  "avatar_url": "https://avatars.githubusercontent.com/u/2?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/defunkt",
  "html_url": "https://github.com/defunkt",
  ...
}
```

avatar_urlの値のhttps://avatars.githubusercontent.com/u/2?v=4 にアクセスすると表示される男性はGitHubのCEO。  

`-i` オプションを付けてリクエストしてみる。
```bash
% curl -i https://api.github.com/users/defunkt
HTTP/2 200
server: GitHub.com
date: Fri, 17 Jun 2022 03:13:16 GMT
content-type: application/json; charset=utf-8
cache-control: public, max-age=60, s-maxage=60
...
```
ステータスラインに`HTTP/2 200`、レスポンスヘッダーに`content-type: application/json; charset=utf-8`が表示されることを確認する。  

次に、個人アクセストークンを使ってプライベートな情報を取得してみる。  

`curl -i -u your_username:your_token https://api.github.com/user`

についてyour_usernameとyour_tokenをそれぞれユーザー名、個人アクセストークンに置き換える。  

```bash
% curl -i -u yoshi-koyama:ghp_h2DilE9yTtEdpFfesxeOoKsL0c2eKEk3jAc https://api.github.com/user
HTTP/2 200
server: GitHub.com
date: Fri, 17 Jun 2022 03:19:18 GMT
content-type: application/json; charset=utf-8
...
{
  "login": "yoshi-koyama",
  ...
}
```

ステータスラインに`HTTP/2 200`、レスポンスヘッダーに`content-type: application/json; charset=utf-8`が表示されること。  
レスポンスボディに自分のユーザー情報が表示されること。  

試しに個人アクセストークンのghp_以降を1文字書き換えてリクエストしてみる。  

```bash
% curl -i -u yoshi-koyama:ghp_e2DilE9yTtEdpFfesxeOoKsL0c2eKEk3jAc https://api.github.com/user
HTTP/2 401
server: GitHub.com
date: Fri, 17 Jun 2022 03:25:22 GMT
content-type: application/json; charset=utf-8
...
{
  "message": "Bad credentials",
  "documentation_url": "https://docs.github.com/rest"
}

```
ステータスラインに`HTTP/2 401`、レスポンスヘッダーに`content-type: application/json; charset=utf-8`が表示されること。  
レスポンスボディに`Bad credentials`という認証情報が正しくないことを意味するメッセージが表示されること。  

Postmanを使って自分のユーザー情報を取得するリクエストを投げてみる。  
<img width="1000" alt="スクリーンショット 2022-06-17 12 46 03" src="https://user-images.githubusercontent.com/62045457/174220865-bd76c2b3-09ce-4db8-af9c-02780d678c47.png">  

レスポンスのステータスコード、ボディ、レスポンスヘッダーにcurlでリクエストしたときと相違ないことを確認する。  
パスワードはGitHubのパスワードではなく個人アクセストークンを設定する。  
※レスポンスヘッダー全ては確認不要です。  
<img width="1000" alt="スクリーンショット 2022-06-17 12 48 03" src="https://user-images.githubusercontent.com/62045457/174221060-880e8b5c-0017-44d7-b3ae-9eb56b9707b8.png">  

<img width="1000" alt="スクリーンショット 2022-06-17 12 48 24" src="https://user-images.githubusercontent.com/62045457/174221076-b3923b5f-bfb7-4ff4-a788-07f34016cc13.png">

ここまで完了したことをSlackにて共有しましょう。  
共有内容は例えば、    
- curlでユーザー情報が表示されること
- Postmanの実行結果のキャプチャ
などがよいです。  

個人アクセストークンやレスポンス内容に個人情報が含まれていないことを気をつけてください。  
レスポンス内容については全部を送る必要はないので適宜省略しましょう。  
また、Slackにてコマンドの実行結果を共有する際にはバッククォート（`）を3つで囲むことによってコードブロックとして整形されますので活用してください。  

## POSTリクエスト

リポジトリの登録をしてみます。  
リクエストヘッダーのghp_から始まる個人アクセストークンは自分用のものに書き換えてください。  
```bash
% curl -i -H "Authorization: token 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" \
    -d '{
        "name": "blog",
        "auto_init": true,
        "private": true,
        "gitignore_template": "nanoc"
      }' \
    https://api.github.com/user/repos
HTTP/2 404
server: GitHub.com
date: Fri, 17 Jun 2022 03:35:08 GMT
content-type: application/json; charset=utf-8
content-length: 147
x-oauth-scopes:
x-accepted-oauth-scopes: public_repo, repo
...

{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest/reference/repos#create-a-repository-for-the-authenticated-user"
}
```

ステータスラインに`HTTP/2 404`、レスポンスヘッダーに`x-accepted-oauth-scopes: public_repo, repo`が表示されていること。  
レスポンスボディの`documentation_url`の値のリンクを読み取るとスコープが足りていないことがわかる。  
> repo scope to create a private repository.

GitHubの個人アクセストークンからスコープを変更する。  
repoにチェックを付ける。  
<img width="1000" alt="スクリーンショット 2022-06-17 12 34 00" src="https://user-images.githubusercontent.com/62045457/174220280-31efd42e-a6e8-4700-b9dc-f7ade0c5e01b.png">

```bash
% curl -i -X POST \
    -H "Authorization: token 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" \
    -d '{
        "name": "blog",
        "auto_init": true,
        "private": true,
        "gitignore_template": "nanoc"
      }' \
    https://api.github.com/user/repos
HTTP/2 201
...
location: https://api.github.com/repos/yoshi-koyama/blog

{
  ...
  "name": "blog",
  ...
  "private": true,
  ...
  "html_url": "https://github.com/yoshi-koyama/blog",
  ...
  "created_at": "2022-06-17T06:51:16Z",
  "updated_at": "2022-06-17T06:51:16Z",
  ...
  "visibility": "private",
  ...
```

ステータスラインが`HTTP/2 201`であること。  
レスポンスヘッダーのlocationの値のリンクがblogリポジトリになっていること。  
created_at（作成日時）、updated_at（更新日時）がリクエストしたときの日時になっていること。  
visibilityの値がprivate、privateの値がtrueになっていること。  
レスポンスボディのhtml_urlの値のリンク先にアクセスするとblogリポジトリが表示されること。  

試しに同じリクエストをもう一度送ってみる。  

```bash
% curl -i -X POST \
    -H "Authorization: token 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" \
    -d '{
        "name": "blog",
        "auto_init": true,
        "private": true,
        "gitignore_template": "nanoc"
      }' \
    https://api.github.com/user/repos
HTTP/2 422
...

{
  "message": "Repository creation failed.",
  "errors": [
    {
      "resource": "Repository",
      "code": "custom",
      "field": "name",
      "message": "name already exists on this account"
    }
  ],
  "documentation_url": "https://docs.github.com/rest/reference/repos#create-a-repository-for-the-authenticated-user"
}
```

ステータスラインが`HTTP/2 422`であること。  
レスポンスボディのmessageに`name already exists on this account`と表示されること。  

またnameを空文字にして送信してみる。  

```bash
% curl -i -H "Authorization: token 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" \
    -d '{
        "name": ""
      }' \
    https://api.github.com/user/repos
HTTP/2 422
...

{
  "message": "Repository creation failed.",
  "errors": [
    {
      "resource": "Repository",
      "code": "missing_field",
      "field": "name"
    },
    {
      "resource": "Repository",
      "code": "custom",
      "field": "name",
      "message": "name is too short (minimum is 1 character)"
    }
  ],
  "documentation_url": "https://docs.github.com/rest/reference/repos#create-a-repository-for-the-authenticated-user"
}
```

ステータスラインが`HTTP/2 422`であること。  
レスポンスボディのmessageに`Repository creation failed.`と表示されること。  
errors内を読むとnameフィールドが空文字であることが原因とわかるようになっていること。  

Postmanを使って自分のユーザー情報を取得するリクエストを投げてみる。  

<img width="1000" alt="スクリーンショット 2022-06-17 13 09 58" src="https://user-images.githubusercontent.com/62045457/174223192-77f61e1e-2f70-4189-8b17-4d48d4b30507.png">  

<img width="1000" alt="スクリーンショット 2022-06-17 13 09 16" src="https://user-images.githubusercontent.com/62045457/174223219-3c900a72-e716-40da-aad6-9e1bd23333db.png">  

レスポンスの確認  

<img width="1217" alt="スクリーンショット 2022-06-17 13 11 35" src="https://user-images.githubusercontent.com/62045457/174223335-19287fae-725e-47e4-bbc9-8e1c46e9222b.png">  

<img width="1217" alt="スクリーンショット 2022-06-17 13 11 21" src="https://user-images.githubusercontent.com/62045457/174223319-f03049e4-c9cb-407f-a324-fab342759b6a.png">  

ここまで完了したことをSlackにて共有しましょう。  
全リクエストの結果を送る必要はなくいくつか試したうちの1つを送信してもらえれば大丈夫です。  

## PATCHリクエスト

作成したリポジトリの下記部分を変更します。
- リポジトリ名をblogからhell-world-blogに変更する
- privateをpublicに変更
- Aboutを"This is your blog repository"に変更
- ホームページに"https://github.com" を設定


下記リクエストはyour_usernameとrepository_nameについて自分のユーザー名と前段階で登録したレポジトリ名（blog）に置き換えてください。  
リクエストヘッダーのghp_から始まる個人アクセストークンも同様です。  
リクエスト内容は [リポジトリの更新API仕様書](https://docs.github.com/ja/rest/repos/repos#update-a-repository) を参考にしています。　  
```bash
% curl -i -X PATCH \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token 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" \
  https://api.github.com/repos/your_username/repository_name \
  -d '{
    "name":"hello-world-blog",
    "description":"This is your blog repository",
    "homepage":"https://github.com",
    "private":false
  }'
HTTP/2 200
server: GitHub.com
...

{
  ...
  "name": "hello-world-blog",
  ...
  "private": false,
  ...
  "html_url": "https://github.com/yoshi-koyama/hello-world-blog",
  "description": "This is your blog repository",
  ...
  "created_at": "2022-06-17T06:00:35Z",
  "updated_at": "2022-06-17T06:02:42Z",
  ...
  "homepage": "https://github.com",
  ...
  "visibility": "public",
  ...
```

ステータスラインがHTTP/2 200であること。  
レスポンスボディを確認して項目が期待通り更新されていること。  

またupdated_atがリクエストした日時になっていること。  

html_urlの値のURLにアクセスすると更新されたリポジトリのトップページが表示されること。  
更新内容が反映されていることをUI上からも確認すること。  

<img width="1636" alt="update-repository" src="https://user-images.githubusercontent.com/62045457/174275159-0bdece8c-ddcb-4c74-b2df-fe6b5544b5a4.png">


試しに、nameを空文字指定して更新のリクエストを投げてみます。  

```bash
% curl -i -X PATCH \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token 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" \
  https://api.github.com/repos/yoshi-koyama/blog \
  -d '{
    "name":""
  }'
HTTP/2 422
...

{
  "message": "Validation Failed",
  "errors": [
    {
      "resource": "Repository",
      "code": "custom",
      "field": "name",
      "message": "name is too short (minimum is 1 character)"
    }
  ],
  "documentation_url": "https://docs.github.com/rest/reference/repos#update-a-repository"
}
```

ステータスラインがHTTP/2 422であること。  
レスポンスボディに"Validation Failed"と表示されていること。  

GitHubのAPIでは入力値が不正であるバリデーションエラーの場合にはHTTPステータスコード422で返すようドキュメントに記載されています。

ここまで試したことをPostmanを使って自分のユーザー情報を取得するリクエストを投げてみましょう。  

ここまで完了したことをSlackにて共有しましょう。  
全リクエストの結果を送る必要はなくいくつか試したうちの1つを送信してもらえれば大丈夫です。  

## まとめ

以上でハンズオンは終了です。  
お疲れさまでした。

もし余裕がある人は作成したリポジトリの削除も試してみてください。

ドキュメントは下記を参考にしましょう。

https://docs.github.com/ja/rest/repos/repos#delete-a-repository  

注意点として、個人アクセストークンのスコープの変更が必要です。

## 参考

今回のハンズオンの参考資料となった公式ドキュメントです。  
- [公式のチュートリアル](https://docs.github.com/ja/rest/guides/getting-started-with-the-rest-api)
- [リポジトリの更新API仕様書](https://docs.github.com/ja/rest/repos/repos#update-a-repository)
- [リポジトリの削除APIの仕様書](https://docs.github.com/ja/rest/repos/repos#delete-a-repository )
