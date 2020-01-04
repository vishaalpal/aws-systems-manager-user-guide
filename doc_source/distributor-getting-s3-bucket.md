# Step 4: Create or Choose an Amazon S3 Bucket<a name="distributor-getting-s3-bucket"></a>

When you create a package by using the **Simple** workflow in the console, you choose an existing S3 bucket to which Distributor uploads your software\. In the **Advanced** workflow, you must upload \.zip files of your software or assets to an S3 bucket before you begin\. Whether you create a package by using the **Simple** or **Advanced** workflows in the console, or by using the API, you must have an S3 bucket before you start creating your package\. As part of the package creation process, Distributor copies your installable software and assets from this bucket to an internal AWS Systems Manager store\. Because the assets are copied to an internal store, you can delete or repurpose your S3 bucket when package creation is finished\.

For more information about how to create a bucket, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For more information about how to run an AWS CLI command to create a bucket, see [https://docs.aws.amazon.com/cli/latest/reference/s3/mb.html](https://docs.aws.amazon.com/cli/latest/reference/s3/mb.html) in the *AWS CLI Command Reference*\.