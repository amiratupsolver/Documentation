---
description: This guide provides a quick tour of Upsolver for first time users.
---

# Upsolver Quickstart in 5 minutes

## Welcome to Upsolver!

When you first log into Upsolver's [free Community Edition](https://app.upsolver.com/signup/free), you will see a link to this guide. It provides you with a quick tour of Upsolver. 

![](../../../../.gitbook/assets/image%20%28221%29.png)

## About this guide

The sample environment provides you with a pre-created[ ](../../../../connecting-data-sources/amazon-aws-data-sources/amazon-s3-data-source/quick-guide-s3-data-source-1.md)[Data Source ](../../../../connecting-data-sources/amazon-aws-data-sources/amazon-s3-data-source/quick-guide-s3-data-source-1.md)that continuously parses data from an Amazon S3 bucket. Upsolver transforms the data and users can query the transformed data with SQL. 

{% hint style="info" %}
The [free Community Edition](https://app.upsolver.com/signup/free) offers limited compute. [Contact Upsolver f](https://www.upsolver.com/schedule-demo)or more compute resources. 
{% endhint %}

## Upsolver Quickstart

Welcome to Upsolver! After signing up and logging in the Community Edition, you will see the Quickstart's welcome screen.  Choose **Sandbox** for this Quickstart.

![](../../../../.gitbook/assets/image%20%28102%29.png)

### Create a Data Source

1. Welcome screen: start your Upsolver journey by creating a Data Source. We have set up sample streaming data in an Amazon S3 bucket with new files constantly being written to the bucket. Note: Upsolver provides many built-in [Data Sources.](https://integ.upsolver.com/inputs/create)  Click **Create Data Source** to connect Upsolver to your data source.

![Welcome to Upsolver!](../../../../.gitbook/assets/image%20%28242%29.png)

2. Define data source bucket: select the Amazon S3 bucket that hosts where your data is located. Leave this option as the default value of **upsolver-tutorial-orders** Note: Upsolver supports all data formats. Click on **NEXT**.

![](../../../../.gitbook/assets/image%20%28240%29.png)

3. Define data source format: Upsolver provides many options to parse your data. The Quickstart provides a subset of the options. Leave all values as default: `GLOBAL PATTERN` is set to `*`. It means Upsolver will parse everything in a S3 bucket. `DATE FORMAT` is `yyyy/MM/dd/HH`. This is how objects are stored in the folders. For example: `s3://upsolver-tutorial-orders/2021/01/16/15/35/<file_name>` 

You may see a sample of the files from defined bucket displayed on the right side of the screen. Optionally, you can identify the time you want to start ingesting from. We will **leave all values as default** and click on **NEXT**.

![](../../../../.gitbook/assets/image%20%28239%29.png)

4. Preview sample data: Upsolver will display a sample of the data being parsed. You may click on each individual event to see a formatted record. Click on **CREATE**.

![](../../../../.gitbook/assets/image%20%28231%29.png)

5. You have successfully created your first data source! 🎉 You will find a list of parsed fields on the left and data demographics and statistics as well. Click on each field to view the field's statistics.

![](../../../../.gitbook/assets/image%20%28234%29.png)

Upsolver offers much more than this Quickstart. [Contact Upsolver ](https://www.upsolver.com/schedule-demo)for a demo or a free POC with more compute power.

### Create a Open Lake data output

1. Start creating the Open Lake Output: now we have a data source defined, click on **NEW OPEN LAKE OUTPUT** on the upper right hand corner to start transforming your data and output to Upsolver's Open Lake.

![](../../../../.gitbook/assets/image%20%28232%29.png)

2. Define the Open Lake Output: provide the data output with a `NAME` and define the `DATA SOURCES(s) -`where the data come from. We will use the data source created in the previous section called `upsolver-tutorials-orders`. For this Quickstart, we will write to a new table in the Open Lake environment. Leave all values as **default** and click on **NEXT**.

![](../../../../.gitbook/assets/image%20%28237%29.png)

### Add fields to your Open Lake output

1. Add the following fields to your output by clicking on the **+** sign next to each field. These fields were parsed automatically when the Data Source was created. Leave **data.netTotal** and **data.salesTax** as **DOUBLE** when you map these fields to the output.

```text
data.buyerEmail
data.orderId
data.netTotal (DOUBLE)
data.salesTax (DOUBLE)
```

This step also allows you to change the name and data type of your fields when you output to your target system. After adding the fields, click on **NEXT**.

![Add fields to your data output](../../../../.gitbook/assets/aiu4jyokwz.gif)

2. Click on **Add Calculated Field** to perform a simple data transformation.

![](../../../../.gitbook/assets/image%20%28238%29.png)

### Perform simple data transformations

Upsolver offers 200+ built-in transformations functions. You may use the UI or SQL to transform your data. Changes will be automatically synced between the two interfaces. **Let's start by transforming data.orderDate to a TIMESTAMP format.** 

1. Transform data.orderDate from a STRING to TIMESTAMP.

* Locate **TO\_DATE** function and click on **SELECT**
* Under **DATETIME**, locate the **data.orderDate** field and give it a **NAME** as **order\_date**. 
* click on **SAVE**. Notice that the calculated field is automatically added to your listed output fields as **data**.**order\_date** with TIMESTAMP data type.

![](../../../../.gitbook/assets/fvi5ljfnnb.gif)

2. We can use the **SQL UI** to add a simple calculation directly in SQL instead of using the UI. Click over to the SQL tab. Note: changes the UI are automatically translated in the SQL statement. Changes in the SQL statement will be automatically reflected in the UI.

![](../../../../.gitbook/assets/image%20%28233%29.png)

3. This step uses the SQL UI to directly calculate the total for each order. Add the following SQL to your pre-generated SQL statement \(note that this aggregation can also be easily performed in the UI instead of SQL\) `data.netTotal + data.salesTax as order_total,`on line 10 and `WHERE data.eventType = 'ORDER'` at the end of the statement. The SQL will look like the following after adding the aggregations and filter.

![](../../../../.gitbook/assets/jkfpuxsfrd.gif)

Below is the full SQL statement for your reference.

```sql
SET partition_date = UNIX_EPOCH_TO_DATE(time);
SET order_date = TO_DATE('data.orderDate');
// GENERATED @ 2021-01-25T18:28:15.707009Z
SELECT PARTITION_TIME(partition_date) AS partition_date:TIMESTAMP,
       time AS processing_time:TIMESTAMP,
       data.buyerEmail AS buyeremail:STRING,
       data.orderId AS orderid:STRING,
       data.netTotal AS nettotal:DOUBLE,
       data.salesTax AS salestax:DOUBLE,
       data.netTotal + data.salesTax as order_total, //add this line
       order_date AS order_date:TIMESTAMP
  FROM "upsolver-tutorials-orders"  
  WHERE data.eventType = 'ORDER' //add this line
```

4. Click on **PREVIEW** to make sure the data is as expected.

![](../../../../.gitbook/assets/pk9rywtezn.gif)

4. Click back to your **UI** view. Note: everything you've changed in SQL is automatically reflected in the UI. We're only scratching the surface of Upsolver data processing capabilities. 

![](../../../../.gitbook/assets/veqfqcaiwo.gif)

### Output processed data to Open Lake

1. Click on **RUN** on the upper right corner. Leave everything as its default values and provide a **TABLE NAME**. Click on **NEXT**.

![](../../../../.gitbook/assets/v26cpup8et.gif)

2. Leave all the values as **default**. Note: you may use the slide bar to choose the time window you want to output your data from. Optionally, you can leave **ENDING AT** as **Never** to continuously stream new data into your Open Lake table. Click on **DEPLOY**.

![](../../../../.gitbook/assets/image%20%28235%29%20%281%29.png)

3. Click on the **PROGRESS** tab to monitor the Data Output status. The output will take about _**1-2 minutes**_ to catchup to its current state. Wait for **OUTPUT PROGRESS** to start turning green.

![](../../../../.gitbook/assets/phfhyhpxr8.gif)

4. The output will take about _**1-2 minutes**_ to catchup to its current event. After the data is caught up under **PROGRESS**, click on **ERRORS** to make sure everything is successful. 

![](../../../../.gitbook/assets/rgvgcj1ijn%20%281%29.gif)

### Explore transformed data in Open Lake worksheets

1. Click the **CREATE WORKSHEET** button on upper right hand corner to start exploring the data that you've transformed and written to a table.

![](../../../../.gitbook/assets/rgvgcj1ijn%20%281%29%20%281%29.gif)

2. Expand the **upsolver** catalog and choose the **sample\_data** schema. Click on the **&lt;table name&gt;** you've written to \(from the step 1\). You will see a sample of of your transformed data!

![](../../../../.gitbook/assets/image%20%28230%29.png)

#### 🎉 Congratulations! You have taken a quick tour of Upsolver.[ Contact us ](https://www.upsolver.com/schedule-demo)to start your Upsolver journey. Happy Upsolving!

## Integrate with your cloud account

You have now explored Upsolver by using sample data. You may integrate with your own cloud environment and start transforming your own data!

### Run CloudFormation stack to integrate

1. Navigate to **INTEGRATE NOW** from your Sandbox environment

![](../../../../.gitbook/assets/image%20%2896%29.png)

2. Choose the cloud provider of your preference. In this example, we're going to use AWS.

![](../../../../.gitbook/assets/image%20%2873%29.png)

{% hint style="info" %}
Make sure you're already logged into your AWS account. If applicable, disable the popup blocker.
{% endhint %}

3. You will arrive at the integration page. Leave everything as default and click on **CONTINUE**. 

![](../../../../.gitbook/assets/image%20%2863%29.png)

4. Scroll down your do the your Create Stack page. **Check** the I acknowledge box and click on **Create stack**.

![](../../../../.gitbook/assets/image%20%2891%29.png)

5. The stack creation process might take a minute or two. Keep refreshing until you see **`CREATE_COMPLETE`**.

![](../../../../.gitbook/assets/image%20%2859%29.png)

6. Navigate back to Upsolver. Now you can start creating data sources and transform your own data! 



