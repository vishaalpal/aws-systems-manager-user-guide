# Using Parameter Store parameters in Lambda functions<a name="ps-integration-lambda-extensions"></a>

Parameter Store, a capability of AWS Systems Manager, provides secure, hierarchical storage for configuration data management and secrets management\. You can store data such as passwords, database strings, Amazon Machine Image \(AMI\) IDs, and license codes as parameter values\. 

To use parameters from Parameter Store in AWS Lambda functions, you can use the AWS Parameters and Secrets Lambda Extension\. This extension retrieves parameter values and caches them for future use\. Using the Lambda extension can reduce your costs by reducing the number of API calls to Parameter Store\. Using the extension can also improve latency because retrieving a cached parameter is faster than retrieving it from Parameter Store\. 

A Lambda extension is a companion process that augments the capabilities of a Lambda function\. An extension is like a client that runs in parallel to a Lambda invocation\. This parallel client can interface with your function at any point during its lifecycle\. For more information about Lambda extensions, see [Lambda Extensions API](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-extensions-api.html) in the *AWS Lambda Developer Guide*\.

The AWS Parameters and Secrets Lambda Extension works for both Parameter Store and Secrets Manager\. To learn how to use the Lambda extension with secrets from Secrets Manager, see [Use AWS Secrets Manager secrets in AWS Lambda functions](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets_lambda.html) in the *AWS Secrets Manager User Guide*\. 

## How the extension works<a name="ps-integration-lambda-extensions-how-it-works"></a>

To use parameters in a Lambda function *without* the Lambda extension, you must configure your Lambda function to receive configuration updates by integrating with the `GetParameter` API action for Parameter Store\.

When you use the AWS Parameters and Secrets Lambda Extension, the extension retrieves the parameter value from Parameter Store and stores it in the local cache\. Then, the cached value is used for further invocations until it expires\. Cached values expire after they pass their time\-to\-live \(TTL\)\. You can configure the TTL value using the `SSM_PARAMETER_STORE_TTL` environment variable, as explained later in this topic\.

If the configured cache TTL has not expired, the cached parameter value is used\. If the time has expired, the cached value is invalidated and the parameter value is retrieved from Parameter Store\.

Also, the system detects parameter values that are used frequently and maintains them in the cache while clearing those that are expired or unused\.

To authorize and authenticate Parameter Store requests, the extension uses the same credentials as those used to run the Lambda function itself\. Therefore, the AWS Identity and Access Management \(IAM\) role used to run the function must have the following permissions to interact with Parameter Store:
+ `ssm:GetParameter` – Required to retrieve parameters from Parameter Store\.
+ `kms:Decrypt` – Required if you are retrieving `SecureString` parameters from Parameter Store\.

For more information, see [AWS Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) in the *AWS Lambda Developer Guide*\.

## Adding the AWS Systems Manager Parameter Store and AWS Secrets Manager extension<a name="ps-integration-lambda-extensions-add"></a>

To use the AWS Parameters and Secrets Lambda Extension, you add the extension to your Lambda function as a layer\. 

You add the extension to your function using the same method as you would for any layer\. For more information, see [Using layers with your Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/invocation-layers.html)\.

To add a layer to your function, follow the steps in [Configuring extensions \(\.zip file archive\)](https://docs.aws.amazon.com/lambda/latest/dg/using-extensions.html#using-extensions-config) in the *AWS Lambda Developer Guide*\. 

In the **Choose a layer** area, choose **Specify an ARN**, and then enter the Amazon Resource Name \(ARN\) for the extension in the AWS Region where you created the Lambda function\. The ARNs for each Region are listed in the following table\. 

**Note**  
The name of the extension in the AWS Lambda console is **AWS\-Parameters\-and\-Secrets\-Lambda\-Extension**\.


****  

| Region | ARN | 
| --- | --- | 
|  US East \(N\. Virginia\)  |  `arn:aws:lambda:us-east-1:177933569100:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  US East \(Ohio\)  |  `arn:aws:lambda:us-east-2:590474943231:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  US West \(N\. California\)  |  `arn:aws:lambda:us-west-1:997803712105:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  US West \(Oregon\)  |  `arn:aws:lambda:us-west-2:345057560386:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Canada \(Central\)  |  `arn:aws:lambda:ca-central-1:200266452380:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(Frankfurt\)  |  `arn:aws:lambda:eu-central-1:187925254637:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(Ireland\)  |  `arn:aws:lambda:eu-west-1:015030872274:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(London\)  |  `arn:aws:lambda:eu-west-2:133256977650:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(Paris\)  |  `arn:aws:lambda:eu-west-3:780235371811:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(Stockholm\)  |  `arn:aws:lambda:eu-north-1:427196147048:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Europe \(Milan\)  |  `arn:aws:lambda:eu-south-1:325218067255:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| China \(Beijing\) |  `arn:aws-cn:lambda:cn-north-1:287114880934:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| China \(Ningxia\) |  `arn:aws-cn:lambda:cn-northwest-1:287310001119:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Hong Kong\)  |  `arn:aws:lambda:ap-east-1:768336418462:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Tokyo\)  |  `arn:aws:lambda:ap-northeast-1:133490724326:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| Asia Pacific \(Osaka\) |  `arn:aws:lambda:ap-northeast-3:576959938190:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Seoul\)  |  `arn:aws:lambda:ap-northeast-2:738900069198:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Singapore\)  |  `arn:aws:lambda:ap-southeast-1:044395824272:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Sydney\)  |  `arn:aws:lambda:ap-southeast-2:665172237481:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Jakarta\)  |  `arn:aws:lambda:ap-southeast-3:490737872127:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Asia Pacific \(Mumbai\)  |  `arn:aws:lambda:ap-south-1:176022468876:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  South America \(São Paulo\)  |  `arn:aws:lambda:sa-east-1:933737806257:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
|  Africa \(Cape Town\)  |  `arn:aws:lambda:af-south-1:317013901791:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| Middle East \(UAE\) | arn:aws:lambda:me\-central\-1:858974508948:layer:AWS\-Parameters\-and\-Secrets\-Lambda\-Extension:2 | 
|  Middle East \(Bahrain\)  |  `arn:aws:lambda:me-south-1:832021897121:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| AWS GovCloud \(US\-East\) |  `arn:aws-us-gov:lambda:us-gov-east-1:129776340158:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 
| AWS GovCloud \(US\-West\) |  `arn:aws-us-gov:lambda:us-gov-west-1:127562683043:layer:AWS-Parameters-and-Secrets-Lambda-Extension:2`  | 

## Configuring AWS Systems Manager Parameter Store and AWS Secrets Manager extension<a name="ps-integration-lambda-extensions-config"></a>

You can configure the extension by changing the following AWS Lambda environment variables\. For more information, see [Using AWS Lambda environment variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html) in the *AWS Lambda Developer Guide*\. 

**Note**  
AWS Lambda records operation details about the Lambda extension and Lambda function in Amazon CloudWatch Logs\. 


****  

| Environment variable | Details | Required | Valid values | Default value | 
| --- | --- | --- | --- | --- | 
|  `AWS_SESSION_TOKEN`  |  Used for validating all requests made by the user as a form of server\-side request forgery \(SSRF\) protection\.  | Yes |  | Null | 
|  `SSM_PARAMETER_STORE_TIMEOUT_MILLIS`  |  Timeout, in milliseconds, for requests to Parameter Store\.   A value of 0 \(zero\) indicates no timeout\.  | No | All whole numbers | 0 \(zero\) | 
|  `SECRETS_MANAGER_TIMEOUT_MILLIS`  |  Timeout, in milliseconds, for requests to Secrets Manager\.   A value of 0 \(zero\) indicates no timeout\.  | No | All whole numbers |  0 \(zero\)  | 
|  `SSM_PARAMETER_STORE_TTL`  |  Maximum valid lifetime, in seconds, of a parameter in the cache before it is invalidated\. A value of 0 \(zero\) indicates that the cache should be bypassed\. This variable is ignored if the value for `PARAMETERS_SECRETS_EXTENSION_CACHE_SIZE` is 0 \(zero\)\.  | No | 0 \(zero\) to 300 s \(Five minutes\) | 300000 ms \(5 minutes\) | 
|  `SECRETS_MANAGER_TTL`  |  Maximum valid lifetime, in seconds, of a secret in the cache before it is invalidated\. A value of 0 \(zero\) indicates that the cache is bypassed\. This variable is ignored if the value for `PARAMETERS_SECRETS_EXTENSION_CACHE_SIZE` is 0 \(zero\)\.   | No | 0 \(zero\) to 300 s \(Five minutes\) | 300000 ms \(5 minutes\) | 
| PARAMETERS\_SECRETS\_EXTENSION\_CACHE\_ENABLED |  Determines whether the cache for the extension is enabled\. Value values: `TRUE \| FALSE`  | No | TRUE \| FALSE | TRUE | 
| PARAMETERS\_SECRETS\_EXTENSION\_CACHE\_SIZE |  The maximum size of the cache in terms of number of items\. A value of 0 \(zero\) indicates that the cache is bypassed\. This variable is ignored if both cache TTL values are 0 \(zero\)\.  | No | 0 \(zero\) to 1000 |  1000  | 
| PARAMETERS\_SECRETS\_EXTENSION\_HTTP\_PORT | The port for the local HTTP server\. | No | 1 \- 65535 |  2773  | 
| PARAMETERS\_SECRETS\_EXTENSION\_MAX\_CONNECTIONS |  Maximum number of connections for the HTTP clients that the extension uses to make requests to Parameter Store or Secrets Manager\. This is a per\-client configuration\.  | No | Minimum of 1; No maximum limit\. |  3  | 
| PARAMETERS\_SECRETS\_EXTENSION\_LOG\_LEVEL | The level of detail reported in logs for the extension\. | No |  `DEBUG \| WARN \| ERROR \| NONE \| INFO`  | INFO | 

## Sample commands for using the AWS Systems Manager Parameter Store and AWS Secrets Manager extension<a name="ps-integration-lambda-extensions-sample-commands"></a>

The examples in this section demonstrate API actions for use with the AWS Systems Manager Parameter Store and AWS Secrets Manager extension\.

### Sample commands for Parameter Store<a name="sample-commands-ps"></a>

The Lambda extension uses read\-only access to the **GetParameter** API action\.

To call this action, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get?name=parameter-path?version=version&label=label&withDecryption={true|false}
```

In this example, *parameter\-path* represents the full parameter name, or the parameter path if the parameter is part of a hierarchy\. *version* and *label* are the selectors available for use with the `GetParameter` action\. This command format provides access to parameters in the standard parameter tier\. 

**Note**  
When using GET calls, parameter values must be encoded for HTTP to preserve special characters\. For example, instead of `/abc/def/hij/`, use `%20/abc%20/def%20/hij%20/`\.

```
GET: /systemsmanager/parameters/get/?name=MyParameter&version=5
```

To call a parameter in a hierarchy, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get?name=%20abc%20def%20hij&label=release
```

To call a public \(global\) parameter, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get/?name=%20aws%20service%20list%20…
```

To make an HTTP GET call to a Secrets Manager secret by using Parameter Store references, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get?name=%20aws%20reference%20secretsmanager%20…
```

To make a call using the Amazon Resource Name \(ARN\) for a parameter, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get?name=arn:aws:ssm:us-east-1:12345678910:parameter%20MyParameter
```

To make a call that accesses a `SecureString` parameter with decryption, make an HTTP GET call similar to the following\.

```
GET: /systemsmanager/parameters/get?name=MyParameter&withDecryption=true
```

You can specify that parameters aren't decrypted by omitting `withDecryption` or explicitly setting it to `false`\. You can also specify either a version or a label, but not both\. If you do, only the first of these that is placed after question mark \(`?`\) in the URL is used\.