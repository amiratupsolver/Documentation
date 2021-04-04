# CDC Data Sources \(Debezium\)

## CDC Data Sources

Upsolver supports ingesting CDC data from relational databases such as MySql or Postgres. Upsolver does this by running a [Debezium Engine](https://debezium.io/documentation/reference/development/engine.html) under the hood to connect to the databases Binlog and ingest any change.

### Event Format

Upsolver will read Debezium Change events with the following fields:

* Before -The state of the row before the change that was applied in the current event. This can be null if this row is new.
* After - The state of the row after the change that was applied in the current event.
* Source - Information about the change event. Including things such as what binlog file it came from, and it's sequence number or position within the file. The Source Table and Database will also be here.
* op - The change type. It can be:
  * 'r' - Read events \(when loading the initial data\)
  * 'c' - Create
  * 'u' - Update
  * 'd' - Delete

Below is a sample event:

```javascript
{
    "time": "04/04/2021, 11:45:00",
    "data": {
        "ts_ms": 1617525879250,
        "op": "c",
        "after": {
            "id": "188283-21202",
            "cost": 4.2,
            "item_id": 10
        },
        "source": {
            "name": "debezium",
            "db": "prod",
            "row": 0,
            "server_id": 0,
            "snapshot": "true",
            "table": "sales",
            "version": "1.4.2.Final",
            "ts_ms": 0,
            "file": "mysql-bin-changelog.008019",
            "pos": 156,
            "connector": "mysql"
        }
    },
    "headers": {
        "head": {
            "X-File-Compression": "Auto Detect (Supports ZIP, GZip, Tar, Snappy and AWS KPL)",
            "index": 1
        }
    }
}
```

This event represents a new row being added to the table. You can tell this by the op type being "c". In this case we added a new sale to the "sales" table in the "prod" database \(can be seen in the "source" information\).



### Supported Databases

Currently the following databases and versions are supported

| Database | Version | AWS RDS Supported? |
| :--- | :--- | :--- |
| MySql | 8 | Yes |
| MySql | 5.7 | Yes |

Some databases may require specific binlog configurations to be used. See the documentation page for creating a CDC data source for your database for info.

