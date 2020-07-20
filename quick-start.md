https://dredd.org/en/latest/quickstart.html#quickstart

- Create api-description.yml
    ```
    swagger: '2.0'
    info:
      version: '1.0'
      title: Example API
      license:
        name: MIT
    host: www.example.com
    basePath: /
    schemes:
      - http
    paths:
      /:
        get:
          produces:
            - application/json; charset=utf-8
          responses:
            '200':
              description: ''
              schema:
                type: object
                properties:
                  message:
                    type: string
                required:
                  - message
- Run container
    ```
    docker run -it -v $PWD:/api -w /api apiaryio/dredd sh
- npm setting
    ```
    npm init
    npm install express --save
- Create app.js
    ```
    var app = require('express')();

    app.get('/', function(req, res) {
      res.json({message: 'Hello World!'});
    })

    app.listen(3000)
- Start app
    ```
    node app.js &
- Start dredd
    ```
    dredd api-description.yml http://127.0.0.1:3000
    pass: GET (200) / duration: 107ms
    complete: 1 passing, 0 failing, 0 errors, 0 skipped, 1 total
    complete: Tests took 121ms