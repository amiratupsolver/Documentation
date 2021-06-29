---
description: >-
  This article provides an introduction to how Upsolver works with change data
  capture data sources.
---

# CDC data sources \(Debezium\)

{% hint style="info" %}
### **What is Debezium?**

Debezium is an open source distributed platform for change data capture. Start it up, point it at your databases, and your apps can start responding to all of the inserts, updates, and deletes that other apps commit to your databases.
{% endhint %}

### CDC Data Sources

Upsolver supports ingesting CDC data from relational databases such as MySQL or PostgreSQL. Upsolver provides CDC capabilities by running a [Debezium Engine](https://debezium.io/documentation/reference/development/engine.html) under the hood to connect to the databases binlog or WAL. The connectors automatically detect and ingest any change.

#### Event Format

Upsolver will read Debezium Change events with the following fields:

* **`before`** -The state of the row before the change that was applied in the current event. This can be null if this row is new.
* **`after`** - The state of the row after the change that was applied in the current event.
* **`source`** - Information about the change event. Including things such as what binlog file it came from, and it's sequence number or position within the file. The Source Table and Database will also be here.
* **`op`** - The change type. The options are:
  * `r` - Read events \(when loading the initial data\)
  * `c` - Create
  * `u` - Update
  * `d` - Delete

#### Example 1:

```javascript
{
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
}
```

This example event represents a new row being added to the table. You can tell this by the op type being `c`. In this case we added a new sale to the `sales` table in the `prod` database \(this can be seen in the `source` information\).

#### Example 2:

```javascript
{
    "ts_ms": 1617525879252,
    "op": "u",
    "before": {
        "id": "188283-21202",
        "cost": 4.2,
        "item_id": 10
    },
    "after": {
        "id": "188283-21202",
        "cost": 5,
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
        "pos": 157,
        "connector": "mysql"
    }
}
```

In this case we received an updated event for the event in Example 1. We can see the old values in `before` and the new updated values in `after`.

#### Supported Databases

Currently the following databases and versions are supported:

| Database | Version | AWS RDS Supported? |
| :--- | :--- | :--- |
| MySQL | 5.6+ | Yes |

Some databases may require specific binlog configurations to be used. See the documentation page for creating a CDC data source for your database for info.

