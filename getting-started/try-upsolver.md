---
description: >-
  This page provides a short guide on signing up for a free account through the
  Upsolver website.
---

# Try Upsolver for free

To help you get started with Upsolver, you can try it out for free. You can choose between [**Upsolver Dedicated Compute**](https://app.upsolver.com/signup) ****and [**Community**](https://app.upsolver.com/signup/free) subscriptions. Both options give you free Upsolver units \(UUs\), units of processing capability per hour based on VM instance type.

Upsolver Dedicated Compute is more flexible, but Upsolver uses compute and S3 storage resources in your AWS account. During the sign-up process you grant Upsolver privileges to access your AWS account.

Upsolver Community is fully resourced; you are not required to supply any compute or storage resources.

You can also use Upsolver through [AWS Marketplace](https://aws.amazon.com/marketplace/pp/B07T8JDQ57?ref_=srh_res_product_title). All of the options will allow you to ingest data to Amazon S3, perform transformations with simple SQL, and output to your favorite database or analytics tools—in minutes.

## Upsolver Community sandbox

1. Go to [https://app.upsolver.com/signup/](https://app.upsolver.com/signup/)

![](../.gitbook/assets/image%20%28241%29.png)

2. Enter your contact details and click **Sign Up**. An email message will be sent to the email account specified for verification.

3. Choose **Sandbox** option on the welcome page.

![](../.gitbook/assets/image%20%28184%29.png)

4. Follow the [5 minute tutorial](start-using-upsolver/upsolver-in-10-minutes/) for an end-to-end quick tour of Upsolver

## Use Upsolver for free

1. Go to [https://app.upsolver.com/signup](https://app.upsolver.com/signup)

![](../.gitbook/assets/image%20%28142%29.png)

2. Choose AWS or Azure \(this example uses AWS\)

![](../.gitbook/assets/image%20%28195%29.png)

3. You may choose between **Deploy to My AWS Account** which deploys Upsolver in your own VPC or **Multi-Tenant Deployment** - Upsolver hosts and manages the compute.  Click [here](upsolver-concepts/deployment-models.md) for more information. If you don't have administrative access, click on [SEND THIS PAGE](https://docs.upsolver.com/upsolver-1/getting-started/try-upsolver#send-this-page).  Make sure you allow appropriate access to your AWS account and click **CONTINUE**.

![](../.gitbook/assets/image%20%28244%29.png)

5. In the **AWS CloudFormation page**, check the **I acknowledge** statement and click **Create Stack**.

![](../.gitbook/assets/image%20%28246%29.png)

7. Once the stack is successfully created \(this will take a couple of minutes; you will see that the status of your stack says `CREATE_COMPLETE` once it is finished\), the **Integration Completed** message appears in Upsolver.

![](../.gitbook/assets/image%20%28243%29.png)

8. It will take a few moments for Upsolver to deploy the cluster to your VPC.

![](../.gitbook/assets/image%20%28245%29.png)

9. Once you see the screen below, the deployment is complete. Happy Upsolving!

![](../.gitbook/assets/image%20%28248%29.png)

## Send this page <a id="send-this-page"></a>

1. Click **SEND THIS PAGE.**

![](../.gitbook/assets/image%20%28253%29.png)

2. \(Optional\) Add any **S3 Buckets Containing Your Data** and **Kinesis Streams Containing Your Data**.

![](../.gitbook/assets/image%20%28250%29.png)

3. Click **Generate Link**.

4. Click **Copy** and then send the link to the relevant team member. This link can be pasted into the browser and used to create the required CloudFormation stack.

![](../.gitbook/assets/image%20%28252%29.png)

