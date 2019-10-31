## docker-compose - Connection between containers

[**Docker compose**](https://docs.docker.com/compose/overview/) - giúp chạy ứng dụng multi-container và kết nối các containers với nhau. File cấu hình docker-compose sử dụng định dạng YAML để định nghĩa các application’s services, sau đó ta sẽ start tất cả services đã cấu hình với một single command.

Hãy xem file docker-compose.yml sau đây, chúng ta sẽ chạy app nodejs kết nối với database mongodb, sử dụng 2 container:

```yaml
version: '3'

services:
  app:
    build:
      context: ./app
    container_name: nodejs_app
    environment:
      MONGO_URL: "mongodb://db/test"
    depends_on:
      - db
    ports:
      - "3000:3000"

  db:
    image: mongo
    ports:
      - "27017:27017"
```

* **version** -- sử dụng cú pháp của docker-compose ở version nào
* **services** -- mỗi service sẽ start một container tương ứng
* **build/image** -- chỉ định thư mục chức Dockerfile để build image hoặc sử dũng image có sẵn
* **depends_on** -- liên kết với container khác
* **ports** -- expose port

Cd vào thư mục **examples/compose-nodejs** và chạy lệnh bên dưới:

```bash
docker-compose up
```

* **docker-compose up -d**: thêm tham số -d để chạy ngầm

Console output:

```
$ docker-compose up
Building app
Step 1/6 : FROM node:argon
argon: Pulling from library/node
3d77ce4481b1: Pull complete
534514c83d69: Pull complete
d562b1c3ac3f: Pull complete
4b85e68dc01d: Pull complete
f6a66c5de9db: Pull complete
7a4e7d9a081d: Pull complete
876b13112871: Pull complete
95d109ce6b5d: Pull complete
Digest: sha256:fab73fccce5abc3fade13a99179884a306aa6c5292a2fc11833ee25ca15c1f85
Status: Downloaded newer image for node:argon
 ---> ef4b194d8fcf
Step 2/6 : COPY [".", "/usr/src/"]
 ---> 77453297bcc4
Step 3/6 : WORKDIR /usr/src
 ---> Running in 0e66e1558d79
Removing intermediate container 0e66e1558d79
 ---> 93c58dfb4f3c
Step 4/6 : RUN npm install
 ---> Running in 581d7c3fa215
npm WARN package.json nodejs-testapp@0.0.1 No README data
mongodb@2.2.36 node_modules/mongodb
├── es6-promise@3.2.1
├── readable-stream@2.2.7 (buffer-shims@1.0.0, inherits@2.0.4, process-nextick-args@1.0.7, util-deprecate@1.0.2, core-util-is@1.0.2, isarray@1.0.0, string_decoder@1.0.3)
└── mongodb-core@2.1.20 (bson@1.0.9, require_optional@1.0.1)
Removing intermediate container 581d7c3fa215
 ---> 6aecb6e011c2
Step 5/6 : EXPOSE 3000
 ---> Running in a4231a690a6c
Removing intermediate container a4231a690a6c
 ---> e2853cdafb07
Step 6/6 : CMD ["node", "index.js"]
 ---> Running in c029f3d74396
Removing intermediate container c029f3d74396
 ---> 65573c348797
Successfully built 65573c348797
Successfully tagged compose-nodejs_app:latest
WARNING: Image for service app was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating compose-nodejs_db_1 ... done
Creating nodejs_app          ... done
Attaching to compose-nodejs_db_1, nodejs_app
db_1   | 2019-10-31T07:46:24.843+0000 I  CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=29fc7d852755
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] db version v4.2.1
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] git version: edf6d45851c0b9ee15548f0f847df141764a317e
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.1.1  11 Sep 2018
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] allocator: tcmalloc
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] modules: none
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] build environment:
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten]     distmod: ubuntu1804
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten]     distarch: x86_64
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten]     target_arch: x86_64
db_1   | 2019-10-31T07:46:24.852+0000 I  CONTROL  [initandlisten] options: { net: { bindIp: "*" } }
db_1   | 2019-10-31T07:46:24.853+0000 I  STORAGE  [initandlisten] 
db_1   | 2019-10-31T07:46:24.853+0000 I  STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
db_1   | 2019-10-31T07:46:24.853+0000 I  STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
db_1   | 2019-10-31T07:46:24.853+0000 I  STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=487M,cache_overflow=(file_max=0M),session_max=33000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000,close_scan_interval=10,close_handle_minimum=250),statistics_log=(wait=0),verbose=[recovery_progress,checkpoint_progress],
db_1   | 2019-10-31T07:46:25.698+0000 I  STORAGE  [initandlisten] WiredTiger message [1572507985:698408][1:0x7f94a4e00b00], txn-recover: Set global recovery timestamp: (0,0)
db_1   | 2019-10-31T07:46:25.721+0000 I  RECOVERY [initandlisten] WiredTiger recoveryTimestamp. Ts: Timestamp(0, 0)
db_1   | 2019-10-31T07:46:25.739+0000 I  STORAGE  [initandlisten] Timestamp monitor starting
db_1   | 2019-10-31T07:46:25.748+0000 I  CONTROL  [initandlisten] 
db_1   | 2019-10-31T07:46:25.748+0000 I  CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
db_1   | 2019-10-31T07:46:25.748+0000 I  CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
db_1   | 2019-10-31T07:46:25.748+0000 I  CONTROL  [initandlisten] 
db_1   | 2019-10-31T07:46:25.750+0000 I  STORAGE  [initandlisten] createCollection: admin.system.version with provided UUID: da3f736f-ad61-4935-adef-23fa08d2eaaf and options: { uuid: UUID("da3f736f-ad61-4935-adef-23fa08d2eaaf") }
db_1   | 2019-10-31T07:46:25.787+0000 I  INDEX    [initandlisten] index build: done building index _id_ on ns admin.system.version
db_1   | 2019-10-31T07:46:25.788+0000 I  SHARDING [initandlisten] Marking collection admin.system.version as collection version: <unsharded>
db_1   | 2019-10-31T07:46:25.788+0000 I  COMMAND  [initandlisten] setting featureCompatibilityVersion to 4.2
db_1   | 2019-10-31T07:46:25.788+0000 I  SHARDING [initandlisten] Marking collection local.system.replset as collection version: <unsharded>
db_1   | 2019-10-31T07:46:25.788+0000 I  STORAGE  [initandlisten] Flow Control is enabled on this deployment.
db_1   | 2019-10-31T07:46:25.788+0000 I  SHARDING [initandlisten] Marking collection admin.system.roles as collection version: <unsharded>
db_1   | 2019-10-31T07:46:25.789+0000 I  STORAGE  [initandlisten] createCollection: local.startup_log with generated UUID: 1a2cda6e-3d28-40dd-b97d-7e59d92acef4 and options: { capped: true, size: 10485760 }
db_1   | 2019-10-31T07:46:25.837+0000 I  INDEX    [initandlisten] index build: done building index _id_ on ns local.startup_log
db_1   | 2019-10-31T07:46:25.838+0000 I  SHARDING [initandlisten] Marking collection local.startup_log as collection version: <unsharded>
db_1   | 2019-10-31T07:46:25.839+0000 I  FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory '/data/db/diagnostic.data'
db_1   | 2019-10-31T07:46:25.843+0000 I  SHARDING [LogicalSessionCacheRefresh] Marking collection config.system.sessions as collection version: <unsharded>
db_1   | 2019-10-31T07:46:25.845+0000 I  STORAGE  [LogicalSessionCacheRefresh] createCollection: config.system.sessions with provided UUID: 71927b50-1291-4348-bb9a-3c9ca162c959 and options: { uuid: UUID("71927b50-1291-4348-bb9a-3c9ca162c959") }
db_1   | 2019-10-31T07:46:25.849+0000 I  NETWORK  [initandlisten] Listening on /tmp/mongodb-27017.sock
db_1   | 2019-10-31T07:46:25.849+0000 I  NETWORK  [initandlisten] Listening on 0.0.0.0
db_1   | 2019-10-31T07:46:25.849+0000 I  NETWORK  [initandlisten] waiting for connections on port 27017
db_1   | 2019-10-31T07:46:25.869+0000 I  INDEX    [LogicalSessionCacheRefresh] index build: done building index _id_ on ns config.system.sessions
db_1   | 2019-10-31T07:46:25.893+0000 I  INDEX    [LogicalSessionCacheRefresh] index build: starting on config.system.sessions properties: { v: 2, key: { lastUse: 1 }, name: "lsidTTLIndex", ns: "config.system.sessions", expireAfterSeconds: 1800 } using method: Hybrid
db_1   | 2019-10-31T07:46:25.893+0000 I  INDEX    [LogicalSessionCacheRefresh] build may temporarily use up to 500 megabytes of RAM
db_1   | 2019-10-31T07:46:25.893+0000 I  INDEX    [LogicalSessionCacheRefresh] index build: collection scan done. scanned 0 total records in 0 seconds
db_1   | 2019-10-31T07:46:25.894+0000 I  INDEX    [LogicalSessionCacheRefresh] index build: inserted 0 keys from external sorter into index in 0 seconds
db_1   | 2019-10-31T07:46:25.899+0000 I  INDEX    [LogicalSessionCacheRefresh] index build: done building index lsidTTLIndex on ns config.system.sessions
db_1   | 2019-10-31T07:46:25.902+0000 I  SHARDING [LogicalSessionCacheReap] Marking collection config.transactions as collection version: <unsharded>
db_1   | 2019-10-31T07:46:26.000+0000 I  SHARDING [ftdc] Marking collection local.oplog.rs as collection version: <unsharded>
nodejs_app | Server listening at *: 3000
```

Hãy mở trình duyệt truy cập vào [địa chỉ http://127.0.0.1:3000/](http://127.0.0.1:3000/).

Stop và remove tất cả các các container đã start bằng docker-compose:

```bash
docker-compose down
```

Tham khảo thêm về [docker-compose cli](https://docs.docker.com/compose/reference/overview/).
