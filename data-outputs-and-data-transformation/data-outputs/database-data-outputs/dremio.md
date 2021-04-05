---
description: >-
  This article provides an introduction to Dremio along with a guide on creating
  an Dremio output using Upsolver.
---

# Dremio

{% hint style="info" %}
### **What is Dremio?**

Dremio is a data lake query engine that delivers  fast query speed and a self-service semantic layer operating directly against a data lake storage.
{% endhint %}

### Prerequisite

You must have Glue Catalog integrated with Dremio. Follow instructions [here](https://docs.dremio.com/data-sources/aws-glue-catalog.html) if you haven't integrated.

## Create a new data output

1. Create a new data output by clicking on **Output** and **New Output**.

![](../../../.gitbook/assets/screen-shot-2020-09-05-at-11.12.59-am.png)

2. Select **Dremio** as your output.

![](../../../.gitbook/assets/image.png)

3. Provide a name for the data output. In this case, we will use the name `dremiotest`. Make sure it’s mapped to an existing data source; then click **Next**.

4. Choose the columns that you want to output to Dremio. By clicking on the plus icon![](../../../.gitbook/assets/screen-shot-2020-08-13-at-5.06.39-pm.png)next to the field name. 

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

6. Make sure an existing Dremio database is available for the output. If not, create a new database. 

7. If you have not configured a glue catalog connection, create a glue catalog connection by clicking on **Create your first Glue Data Catalog Connection**

![](../../../.gitbook/assets/image%20%2850%29.png)

8. Fill out the Dremio database and table information for output then click **Next**.

9. Choose the time period for the data that you want to load then click **Deploy**.

