# postgres fdw clickhouse

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
CREATE EXTENSION clickhousedb_fdw;

CREATE SERVER myserver FOREIGN DATA WRAPPER clickhousedb_fdw OPTIONS(dbname 'default', driver '/opt/clickhousedb/libclickhouseodbc.so', host 'server');

CREATE USER MAPPING FOR postgres SERVER myserver;

CREATE FOREIGN TABLE wikistat (
	project text,
	subproject text,
	hits int,
	size int
) SERVER myserver OPTIONS (table_name 'wikistat');

select * from wikistat;
```