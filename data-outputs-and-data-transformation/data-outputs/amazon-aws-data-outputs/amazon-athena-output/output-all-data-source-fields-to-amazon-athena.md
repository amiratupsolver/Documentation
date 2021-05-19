---
description: >-
  This guide provides instructions on how to write all fields to an Athena
  output by using SELECT *
---

# Output all data source fields to Amazon Athena

## Background

Sometimes it is necessary to read a large number of fields from a data source and write these fields to Athena without any transformations.

{% hint style="info" %}
This feature works well with a [CDC data source](../../../../connecting-data-sources/cdc-data-sources-debezium/mysql-cdc-data-sourcei.md).
{% endhint %}

## Create Amazon Athena output

1. Create an Athena output by clicking on **OUTPUTS &gt; NEW &gt; SELECT** Amazon Athena

![](../../../../.gitbook/assets/image%20%28255%29.png)

2. Choose **Hierarchical** and click on **NEXT**.

![](../../../../.gitbook/assets/image%20%28260%29.png)

3. Navigate to the SQL mode screen by clicking on **SQL** on the upper right corner.

![](../../../../.gitbook/assets/bi6z9gps7m.gif)

## SELECT all fields from data source

Add `data.* as *` to your `SELECT` statement.

![](../../../../.gitbook/assets/image%20%28258%29.png)

## Run the output

1. Click on **RUN** on the upper right corner

![](../../../../.gitbook/assets/image%20%28262%29.png)

2. Enter the Athena information and click on **NEXT**

![](../../../../.gitbook/assets/image%20%28256%29.png)

3. Choose your time range for your data. Never means a continuous stream. Click on **DEPLOY**.

![](../../../../.gitbook/assets/image%20%28261%29.png)

## Verify data in Amazon Athena

![](../../../../.gitbook/assets/image%20%28263%29.png)

