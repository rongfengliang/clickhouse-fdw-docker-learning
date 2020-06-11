# postgres fdw clickhouse

> with https://github.com/adjust/clickhouse_fdw

## clickhouse data init

* create table

```code
CREATE TABLE wikistat
(
    project String,
    subproject String,
    hits UInt64,
    size UInt64
) ENGINE = Log;
```

* import data

use tools or ui

```code
serverhost=192.168.0.103 // replace with your hostid
docker run -i yandex/clickhouse-client  --format_csv_delimiter="|" --host ${serverhost} --query="INSERT INTO default.wikistat FORMAT CSV" < ./data/info.csv
```

## use clickhouse fdw

```code
CREATE EXTENSION clickhouse_fdw;

CREATE SERVER clickhouse_svr FOREIGN DATA WRAPPER clickhouse_fdw OPTIONS(dbname 'default', host 'server');

CREATE USER MAPPING FOR postgres SERVER clickhouse_svr
       OPTIONS (user 'default', password '');
       
IMPORT FOREIGN SCHEMA "default" FROM SERVER clickhouse_svr INTO public;

select * from wikistat;
```