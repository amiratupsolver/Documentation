# MySQL CDC data source

Currently the following MySQL databases versions are supported: 5.7, 8. This includes managed DB instances via AWS RDS.

### Prerequisites

#### Permissions

In order for Upsolver / Debezium to read the binlog and the initial state of the database we require the following permissions:

* `SELECT` \(On the tables that are to be loaded, usually all tables\)
* `REPLICATION CLIENT`
* `REPLICATION SLAVE`
* `RELOAD`
* `SHOW DATABASES`

For more information about creating such a user see here: [https://debezium.io/documentation/reference/connectors/mysql.html\#setting-up-mysql](https://debezium.io/documentation/reference/connectors/mysql.html#setting-up-mysql)

#### Enabled binlog

The binlog must be enabled in `ROW` mode. You can follow these instructions to check if it's enabled and configure it if you are using your own MySQL server: [https://debezium.io/documentation/reference/connectors/mysql.html\#enable-mysql-binlog](https://debezium.io/documentation/reference/connectors/mysql.html#enable-mysql-binlog)

If you are using AWS RDS you will need to:

1. Make sure that [Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) are enabled. If they are not enabled the binlog will be disabled regardless of the parameter group settings.
2. Change the binlog mode to `ROW` in the parameter group settings for the cluster:
   1. Create new parameter group based on the base group that matches your DB version.
   2. Change `binlog_format` to `ROW`
   3. Change the DB Cluster to use the new parameter group and apply the changes. 

### Creating a MySQL CDC Data Source

1. In the Data Sources page click **New** in the top right hand corner
2. **Select** MySql \(Debezium\)
3. **Name** the Data Source
4. **Select** the **compute cluster** you want the Data Source to run on.  Note: This Compute cluster must have access to the database. If the database has security settings restricting traffic please ensure the compute cluster is using elastic IP's and that they are open in the security settings. Alternatively, you can set up [VPC peering](../../getting-started/upsolver-aws-deployment-guide/vpc-peering.md) between the Upsolver VPC and the VPC hosting the database if the DB does not allow public traffic.
5. **Select** the **target storage location**. This is the location we will store the raw CDC events that were read from the binlog. 
6. Optionally set a retention value if you want to keep data only for some period of time. You can always change this later in the properties.
7. Set the **host name** of the **database**. This is usually a DNS record name pointing the the database. 
8. Set the **port** for your connection. The default for MySQL is **3306**.
9. Set the **username** and **password** to connect to the database with. Note: Make sure the user has the permissions listed in the prerequisite section of this page. 
10. Optional:
    1. Set a list of regular expressions of databases to include. If left empty all databases will be included. 
    2. Set a list of regular expressions of databases to exclude. If left empty no databases will be excluded.
    3. Set a list of regular expressions of tables to **include**. If empty, **all** tables are included.
    4. Set a list of regular expressions of tables to **exclude**. If empty, **no** tables are excluded.

