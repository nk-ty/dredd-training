https://dredd.org/en/latest/hooks/index.html#hooks

## Hooks are usually used for:
* Loading database fixtures,
* cleaning up after test step(s),
* handling auth and sessions,
* passing data between transactions (saving state from responses),
* modifying a request generated from the API description,
* changing generated expectations,
* setting custom expectations,
* debugging by logging stuff.


## Getting started


1. Create Swagger yaml
  記事一覧と新しい記事を発行するAPIを定義
  新しい記事の発行（POST）には認証キーが必要とする

  ```
  swagger: "2.0"
  info:
    title: "Blog API"
    version: "1.0"
  consumes:
    - "application/json; charset=utf-8"
  produces:
    - "application/json; charset=utf-8"
  paths:
    "/articles":
      x-summary: "Articles"
      get:
        summary: "List articles"
        description: "Retrieve a list of all articles"
        responses:
          200:
            description: "Articles list"
            examples:
              "application/json; charset=utf-8":
                - id: 1
                  title: "Creamy cucumber salad"
                  text: "Slice cucumbers…"
      post:
        summary: "Publish an article"
        description: "Create and publish a new article"
        parameters:
          - name: "body"
            in: "body"
            schema:
              example:
                title: "Crispy schnitzel"
                text: "Prepare eggs…"
        responses:
          201:
            description: "New article"
            examples:
              "application/json; charset=utf-8":
                id: 2
                title: "Crispy schnitzel"
                text: "Prepare eggs…"
2. Create hooks.js
  フックを使用して、DreddのリクエストにAuthorizationヘッダーを追加する
  ```
  const hooks = require('hooks');

  hooks.before('Articles > Publish an article > 201 > application/json; charset=utf-8', (transaction) => {
      transaction.request.headers.Authorization = 'Basic: hogehogesecret';
      console.log('hooks.before');
  });
3. Run
  dredd.ymlのblueprint:を変更しないと定義どおりにテストされないので注意
  APIがないのでエラーになるがCosole.logとAuthorizationヘッダーは確認できた
  ```
  dredd ./api-description-for-hook.yml http://127.0.0.1:3000 --hookfiles=./hooks.js
  fail: GET (200) /articles duration: 390ms
  hooks.before
  fail: POST (201) /articles duration: 52ms
  info: Displaying failed tests...
  fail: GET (200) /articles duration: 390ms
  fail: headers: At '/content-type' No enum match for: "text/html; charset=utf-8"
  body: Can't validate actual media type 'text/plain' against the expected media type 'application/json'.
  statusCode: Expected status code '200', but got '404'.

  request: 
  method: GET
  uri: /articles
  headers: 
      Content-Type: application/json; charset=utf-8
      Accept: application/json; charset=utf-8
      User-Agent: Dredd/13.1.2 (Linux 4.19.76-linuxkit; x64)

  body: 



  expected: 
  headers: 
      Content-Type: application/json; charset=utf-8

  body: 
  [
    {
      "id": 1,
      "title": "Creamy cucumber salad",
      "text": "Slice cucumbers…"
    }
  ]
  statusCode: 200


  actual: 
  statusCode: 404
  headers: 
      x-powered-by: Express
      content-security-policy: default-src 'none'
      x-content-type-options: nosniff
      content-type: text/html; charset=utf-8
      content-length: 147
      date: Wed, 22 Jul 2020 01:57:17 GMT
      connection: close

  bodyEncoding: utf-8
  body: 
  <!DOCTYPE html>
  <html lang="en">
  <head>
  <meta charset="utf-8">
  <title>Error</title>
  </head>
  <body>
  <pre>Cannot GET /articles</pre>
  </body>
  </html>




  fail: POST (201) /articles duration: 52ms
  fail: headers: At '/content-type' No enum match for: "text/html; charset=utf-8"
  body: Can't validate actual media type 'text/plain' against the expected media type 'application/json'.
  statusCode: Expected status code '201', but got '404'.

  request: 
  method: POST
  uri: /articles
  headers: 
      Content-Type: application/json; charset=utf-8
      Accept: application/json; charset=utf-8
      User-Agent: Dredd/13.1.2 (Linux 4.19.76-linuxkit; x64)
      Authorization: Basic: hogehogesecret

  body: 
  {
    "title": "Crispy schnitzel",
    "text": "Prepare eggs…"
  }


  expected: 
  headers: 
      Content-Type: application/json; charset=utf-8

  body: 
  {
    "id": 2,
    "title": "Crispy schnitzel",
    "text": "Prepare eggs…"
  }
  statusCode: 201


  actual: 
  statusCode: 404
  headers: 
      x-powered-by: Express
      content-security-policy: default-src 'none'
      x-content-type-options: nosniff
      content-type: text/html; charset=utf-8
      content-length: 148
      date: Wed, 22 Jul 2020 01:57:18 GMT
      connection: close

  bodyEncoding: utf-8
  body: 
  <!DOCTYPE html>
  <html lang="en">
  <head>
  <meta charset="utf-8">
  <title>Error</title>
  </head>
  <body>
  <pre>Cannot POST /articles</pre>
  </body>
  </html>




  complete: 0 passing, 2 failing, 0 errors, 0 skipped, 2 total
  complete: Tests took 781ms