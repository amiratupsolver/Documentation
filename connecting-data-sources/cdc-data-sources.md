# CDC Data Sources

Upsolver supports ingesting CDC data from relational databases such as MySql or Postgres. Upsolver does this by running [Debezium Engine](https://debezium.io/documentation/reference/development/engine.html) under the hood to connect to the databases Binlog and ingest any change.

## Event Format

Upsolver will read Debezium Change events with the following fields:

* Before - This is the state of the row before the change that was applied in the current event. This can be null if this row is new.
* After - This is the state of the row after the change that was applied in the current event.
* Source - This is information about the change event. Including things such as what binlog file it came from, and it's sequence number or position within the file. The Source Table and Database will also be here.
* op - This is the change type. It can be:
  * 'r' - Read events \(when loading the initial data\)
  * 'c' - Create
  * 'u' - Update
  * 'd' - Delete

Below is a sample event:



