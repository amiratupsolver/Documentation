---
description: >-
  This article provides an introduction to PrestoDB along with a guide on
  creating an PrestoDB output using Upsolver.
---

# PrestoDB

{% hint style="info" %}
### **What is PrestoDB?**

Presto is an open source distributed SQL query engine for running interactive analytic queries against data sources of all sizes ranging from gigabytes to petabytes.
{% endhint %}

## Create a new data output

1. Create a new data output by clicking on **Output** and **New Output**.

![](../../../.gitbook/assets/screen-shot-2020-09-05-at-11.12.59-am.png)

2. Select **PrestoDB** as your output type.

3. Provide a name for the data output. In this case, we will use the name `prestodbtest`. Make sure it’s mapped to an existing data source; then click **Next**.

4. Choose the columns that you want to output to PrestoDB. By clicking on the plus icon![](../../../.gitbook/assets/screen-shot-2020-08-13-at-5.06.39-pm.png)next to the field name. 

Add the following columns: 

* `aged_70_older` 
* `continent` 
* `date` 
* `new_cases`
* `population` 

![](https://www.upsolver.com/wp-content/uploads/2020/07/Screen-Shot-2020-07-07-at-13.21.59.png)

{% hint style="info" %}
There are many powerful capabilities available such as **aggregations**, **filters**, **calculations**, **lookup tables for joining**, ****and more. These features will be covered on a later guide. 
{% endhint %}

5. Click **Preview** to check that the output data is as expected, then click **Run**.

![](../../../.gitbook/assets/screen-shot-2020-09-05-at-11.16.29-am.png)

6. Make sure an existing PrestoDB database is available for the output. If not, create a new database. 

7. Fill out the PrestoDB database and table information for output then click **Next**.

8. Choose the time period for the data that you want to load then click **Deploy**.

## 

