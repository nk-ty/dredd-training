- docker run -it -v $PWD:/api -w /api apiaryio/dredd sh
- create api-description.yml
- npm init
- npm install express --save
- create app.js
- start app
    ```
    node app.js &
- start dredd
    ```
   dredd api-description.yml http://127.0.0.1:3000
   pass: GET (200) / duration: 107ms
   complete: 1 passing, 0 failing, 0 errors, 0 skipped, 1 total
   complete: Tests took 121ms