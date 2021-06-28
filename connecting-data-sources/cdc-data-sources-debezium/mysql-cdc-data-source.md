---
description: >-
  This article provides an introduction to how Upsolver works with MySQL change
  data capture data sources.
---

# MySQL CDC data source

## Supportability

MySQL database versions 5.6+ are supported. This includes managed DB instances hosted by AWS RDS.

## Prerequisites

### Permissions

In order for Upsolver to read the binlog and the initial state of the database the CDC data source requires the following permissions:

* `SELECT` \(On the tables that are to be loaded, usually all tables\)
* `REPLICATION CLIENT`
* `REPLICATION SLAVE`
* `RELOAD`
* `SHOW DATABASES` \(this is only necessary if the server was started with the --skip-show-database option\)

For more information about creating such a user see here: [https://debezium.io/documentation/reference/connectors/mysql.html\#setting-up-mysql](https://debezium.io/documentation/reference/connectors/mysql.html#setting-up-mysql)

### Enabled binlog

The binlog must be enabled in `ROW` mode. You can follow these instructions to check if it's enabled and configure it if you are using your own MySQL server: [https://debezium.io/documentation/reference/connectors/mysql.html\#enable-mysql-binlog](https://debezium.io/documentation/reference/connectors/mysql.html#enable-mysql-binlog)

If you are using **AWS RDS** you will need to:

1. Make sure that [Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) are enabled. If they are not enabled the binlog will be disabled regardless of the parameter group settings.
2. Change the binlog mode to `ROW` in the parameter group settings for the cluster:
   1. Create new parameter group based on the base group that matches your DB version.
   2. Change `BINGLOG_FORMAT` to `ROW`
   3. Change the DB Cluster to use the new parameter group and apply the changes. 

### Ensure binlog duration is long enough

Upsolver constantly reads the binlog's latest events, so generally the binlog retention only needs to be set high enough to handle server interruptions or situations where the data source or compute cluster is paused in Upsolver. It is therefore recommended to set the binlog retention to 1 day, and generally not recommended to set it below 3 hours.

Using AWS RDS this can be achieved using the following command:

> call mysql.rds\_set\_configuration\('binlog retention hours', 24\);

For custom deployments please consult the documentation for your versions of MySQL.

## Creating a MySQL CDC Data Source with Amazon Athena Output

1. Click on **DATA SOURCES** and click on **NEW** on the upper right hand corner

2. **SELECT** MySQL from the **Change Data Capture** group

![](../../.gitbook/assets/image%20%28249%29.png)

3. Enter your connection string for MySQL database. Make sure to use the right port, user name and password. Click on **TEST CONNECTION** and make surer the **green check mark** appears.

![](../../.gitbook/assets/image%20%2824%29.png)

3. \(Optional\) Choose the schema\(s\) and table\(s\) that you want to read from. The list supports regular expression. You may also exclude columns from your tables.

![](../../.gitbook/assets/image%20%2811%29.png)

4. \(Optional\) define destination information. This will replicate the database automatically to the target data lake. TABLE PREFIX will show up as part of your destination table name. For example, if you input `staging_` for a table named `orders`. The destination table name will be `staging_orders` 

![](../../.gitbook/assets/image%20%28268%29.png)

5. Give a name to your data source and data output. This will be the name of the entity in Upsolver. Click on **CONTINUE**. 

![](../../.gitbook/assets/image%20%2818%29.png)

## Monitoring

You may monitor the ingestion progress by clicking on the **CDC Data source &gt; MONITORING &gt; SNAPSHOT STATUS**

![](../../.gitbook/assets/image%20%28264%29.png)

## Creating an Amazon Athena Output to Multiple Tables \(split by key\)

1. Click on OUTPUTS and NEW on the upper right hand corner.

2. Select the Athena data output.

![](../../.gitbook/assets/image%20%28265%29.png)

3. Give the output a name. Choose the CDC data source that you've defined from the previous section. Make sure to choose **Hierarchical** and click on **NEXT**.

![](../../.gitbook/assets/image%20%28269%29.png)

4. Click on the **SQL view** and add the `data.* as *` to your SQL statement. 

![](../../.gitbook/assets/image%20%28118%29.png)

5. Click back to the **UI view** and click on **More** and select **Manage Multi Table** 

![](../../.gitbook/assets/image%20%28206%29.png)

6. Select the column that you want to use as the key for splitting data into multiple tables.Click on **CLOSE**.

![](../../.gitbook/assets/image%20%286%29.png)

7. Click on RUN on the upper right hand corner

![](../../.gitbook/assets/image%20%28267%29.png)

8. Fill out your run parameters and make sure to define TABLE NAME prefix. If the MySQL table is named `orders`, it will show up as `split_orders` in Athena with the example given below. Click on **NEXT**.

![](../../.gitbook/assets/image%20%28241%29.png)

9. Define how you would like to run this deployment. Keep in mind that leaving `ENDING AT` as `Never` will continuously read updates from your MySQL source.

![](../../.gitbook/assets/image%20%28266%29.png)

10. Wait for the data to be written to Athena and you're done! 

![](../../.gitbook/assets/image%20%2869%29.png)

