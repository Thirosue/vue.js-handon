## サーバの起動

一覧画面を作成するためのサーバを起動する

### 起動ファイルを作成

新規ファイルを作成して、以下内容をコピぺし、 `docker-compose.yml` の名称で保存する

```:docker-compose.yml
---
version: '2'

services:
  sample-db:
    image: mirrored1976/sample-db:latest
    container_name: sample-db
    hostname: "sample-db"
    environment:
      - MYSQL_DATABASE=sample
      - MYSQL_ROOT_USER=root
      - MYSQL_ROOT_PASSWORD=passw0rd
      - TZ=Japan
    networks:
      - link
    ports:
      - "13306:3306"

  sample-api:
    image: mirrored1976/sample-api:latest
    container_name: sample-api
    hostname: "sample-api"
    networks:
      - link
    ports:
      - "18081:18081"
    entrypoint: java -Dspring.datasource.url=jdbc:mysql://sample-db:3306/sample?useSSL=false&characterEncoding=UTF-8 -jar /sample-web-admin.jar
    depends_on:
      - sample-db

networks:
  link:
    driver: bridge
```

### サーバー起動

以下コマンドでサーバーを起動する

```
$ docker-compose up -d
```

アプリケーションをdownloadし、起動する

```
$ docker-compose up -d
Creating network "vuejs-handon_link" with driver "bridge"
Pulling sample-db (mirrored1976/sample-db:latest)...
latest: Pulling from mirrored1976/sample-db
fc7181108d40: Pull complete
787a24c80112: Pull complete
a08cb039d3cd: Pull complete
4f7d35eb5394: Pull complete
5aa21f895d95: Pull complete
a742e211b7a2: Pull complete
0163805ad937: Pull complete
62d0ebcbfc71: Pull complete
559856d01c93: Pull complete
c849d5f46e83: Pull complete
f114c210789a: Pull complete
aa9080d045d2: Pull complete
2b4ef40881e6: Pull complete
ea4fda1172a9: Pull complete
9bc367843304: Pull complete
Pulling sample-api (mirrored1976/sample-api:latest)...
latest: Pulling from mirrored1976/sample-api
e7c96db7181b: Pull complete
f910a506b6cb: Pull complete
c2274a1a0e27: Pull complete
e05e14490bdb: Pull complete
Creating sample-db ... done
Creating sample-api ... done
```

`docker ps` コマンドで以下の状態になっていれば正常に起動している

```
$ docker ps
CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS              PORTS                                NAMES
f97b8be09fb0        mirrored1976/sample-api:latest   "java -Dspring.datas…"   44 seconds ago      Up 43 seconds       0.0.0.0:18081->18081/tcp             sample-api
0219f38e21bd        mirrored1976/sample-db:latest    "docker-entrypoint.s…"   45 seconds ago      Up 44 seconds       33060/tcp, 0.0.0.0:13306->3306/tcp   sample-db
```

### APIの起動確認

http://localhost:18081/admin/swagger-ui.html に接続し、APIの起動を確認する

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/server_1.png "")

`staff-rest-controller` を選択し、

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/server_2.png "")

一番上の、`GET /api/staff` をドリルダウンする。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/server_3.png "")

`Try it out!` ボタンを押して、APIをコールする

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/server_4.png "")

APIが正常終了することを確認する

`docker logs sample-api` コマンドを発行すると、SQLが発行され、APIがコールされていることが確認できる

```
$ docker logs sample-api
```

* APIログ (Spring Boot)

```
（....中略....）

2019-07-27 13:21:01.758 [API_STAFF:457105673:GUEST]  INFO 1 --- [qtp899929247-12] o.s.doma.jdbc.UtilLoggingJdbcLogger      : [DOMA2076] SQLログ : SQLファイル=[META-INF/com/sample/domain/dao/system/StaffDao/selectAll.sql],
select sql_calc_found_rows
  created_by, created_at, updated_by, updated_at, deleted_by, deleted_at, version, staff_id, password, first_name, last_name, email, tel, password_reset_token, token_expires_at
FROM
  staffs
WHERE
  deleted_at IS NULL







 limit 0, 10
2019-07-27 13:21:01.761 [API_STAFF:457105673:GUEST]  INFO 1 --- [qtp899929247-12] o.s.doma.jdbc.UtilLoggingJdbcLogger      : [DOMA2076] SQLログ : SQLファイル=[null],
select found_rows()
2019-07-27 13:21:01.762 [API_STAFF:457105673:GUEST]  INFO 1 --- [qtp899929247-12] o.s.doma.jdbc.UtilLoggingJdbcLogger      : [DOMA2221] EXIT   : クラス=[com.sample.domain.dao.system.StaffDaoImpl], メソッド=[selectAll]
2019-07-27 13:21:01.764 [API_STAFF:457105673:GUEST]  INFO 1 --- [qtp899929247-12] c.s.w.b.aop.RequestTrackingInterceptor   : path=/admin/api/staff, method=GET, Elapsed 11ms.
2019-07-27 13:21:01.859 [::]  INFO 1 --- [pool-1-thread-1] com.sample.web.admin.task.CacheClear     : clear expired cache
```