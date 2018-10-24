## Build Parse server use Docker


### Install docker 
If your computer doesn't install docker please install frist, might can be found [here](https://docs.docker.com/install/).

Make your own `MASTER_KEY` and `APPLICATION_ID `

```
#For Example 

MASTER_KEY:17cbbaa6-3c68-11e8-b467-0ed5f89f718b
APPLICATION_ID:testapp

```

### Create docker images from github 
 
Fetch the latest source code of parse server and dashboard from github and build docker images. 

Build parse server docker image:
```
docker build https://github.com/parse-community/parse-server.git -t parse-server
```

Build parse dashboard docker image:
```
docker build https://github.com/parse-community/parse-dashboard.git -t parse-dashboard
```

You could try to use docker command `docker images` to check builds of those images have be successed.


### Mongo
Setting MongoDB server port and docker container name.

```
docker run -d -p 27017:27017 --name my-mongo -d mongo

```

### Parse server
To active the LiveQuery server, should changed setting `--startLiveQueryServer` to `true` and class objects will be using LiveQuery, need to set `--liveQuery` with your class names like `'{"classNames":["Message","Group"]}'`

If you want to know more parse server environment setting, [here](https://github.com/parse-community/parse-server/blob/master/src/Options/Definitions.js) are environment definitions. 

```
docker run -p 1337:1337 --name my-parse-server \
                        --link my-mongo:mongo \
                        -d parse-server \
                        --appId testapp \
                        --masterKey 17cbbaa6-3c68-11e8-b467-0ed5f89f718b \
                        --databaseURI mongodb://mongo/test \
                        --startLiveQueryServer true \
                        --liveQuery '{"classNames":["Message","Group"]}'
```

### Testing Parse sever 

```
curl -X POST \
-H "X-Parse-Application-Id: testapp" \
-H "Content-Type: application/json" \
-d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
http://localhost:1337/parse/classes/GameScore
```

### Parse dashboard
Setting administer login username and password, setting  of others. Allow use http to access should set `PARSE_DASHBOARD_ALLOW_INSECURE_HTTP` to `true`

```
docker run -d \
        -e PARSE_DASHBOARD_CONFIG='{"apps":[{"appId":"testapp","serverURL":"http://localhost:1337/parse","masterKey":"17cbbaa6-3c68-11e8-b467-0ed5f89f718b","appName":"TestApp"}],"users":[{"user":"admin","pass":"admin"}]}' \
        -e PARSE_DASHBOARD_ALLOW_INSECURE_HTTP=1  \
        -p 4040:4040                      \
        --link my-parse-server            \
        --name my-parse-dashboard            \
        parse-dashboard
```
### URL

Parse Server URL: http://localhost:1337/parse

Parse Dashboard URL: http://localhost:4040/apps
