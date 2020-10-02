# Kinesis producer for HSL realtime vehicle position data
Modified from Amazon's Kinesis Solution template, to test Kinesis Streams and other Kinesis services
using [HSL's vehicle position MQTT API](https://digitransit.fi/en/developers/apis/4-realtime-api/vehicle-positions/)
The CloudFormation stack sets up an EC2 instance and a Kinesis Stream, as well as relevant roles. 
The producer Java application will need to be started manually after deploying the stack.

## Deployment
You can launch this solution with one click from the [solution home page](https://aws.amazon.com/solutions/implementations/aws-streaming-data-solution-for-amazon-kinesis).

> **Please ensure you test the templates before updating any production deployments.**

## Creating a custom build
To customize the solution, follow the steps below:

### Prerequisites
* [AWS Command Line Interface](https://aws.amazon.com/cli/)
* Node.js 12.x or later
* Python 3.8 or later
* Java 1.8 (only required if using Apache Flink)
* Apache Maven 3.1 (only required if using Apache Flink)

> **Note**: The commands listed below will build all patterns. To only include one, you can modify the CDK entrypoint file on `source/bin/streaming-data-solution.ts`

### 1. Download or clone this repo
```
git clone https://github.com/awslabs/aws-streaming-data-solution-for-amazon-kinesis
```

### 2. After introducing changes, run the unit tests to make sure the customizations don't break existing functionality
```
cd ./source
chmod +x ./run-all-tests.sh
./run-all-tests.sh
```

### 3. Build the solution for deployment
> **Note**: In order to compile the solution, the _build-s3_ will install the AWS CDK.

```
ARTIFACT_BUCKET=my-bucket-name     # S3 bucket name where customized code will reside
SOLUTION_NAME=my-solution-name     # customized solution name
VERSION=my-version                 # version number for the customized code

cd ./deployment
chmod +x ./build-s3-dist.sh
./build-s3-dist.sh $ARTIFACT_BUCKET $SOLUTION_NAME $VERSION
```

> **Why doesn't the solution use CDK deploy?** This solution includes a few Lambda functions, and by default CDK deploy will not install any dependencies (it'll only zip the contents of the path specified in _fromAsset_). In future releases, we'll look into leveraging bundling assets using [Docker](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-lambda-readme.html#bundling-asset-code).

> In addition to that, there are also some extra components (such as the demo applications for the KPL and Kinesis Data Analytics) that are implemented in Java, and the _build-s3_ script takes care of packaging them.

### 4. Upload deployment assets to your Amazon S3 buckets
Create the CloudFormation bucket defined above, as well as a regional bucket in the region you wish to deploy. The CloudFormation templates are configured to pull the Lambda deployment packages from Amazon S3 bucket in the region the template is being launched in.

```
aws s3 mb s3://$ARTIFACT_BUCKET --region eu-central-1
aws s3 mb s3://$ARTIFACT_BUCKET-eu-central-1 --region eu-central-1
```

```
aws s3 sync ./global-s3-assets s3://$ARTIFACT_BUCKET/$SOLUTION_NAME/$VERSION --acl bucket-owner-full-control
aws s3 sync ./regional-s3-assets s3://$ARTIFACT_BUCKET-eu-central-1/$SOLUTION_NAME/$VERSION --acl bucket-owner-full-control
```

### 5. Launch the CloudFormation template
* Get the link of the template uploaded to your Amazon S3 bucket (created as $ARTIFACT_BUCKET in the previous step)
* Deploy the solution to your account by launching a new AWS CloudFormation stack

## Additional Resources

### Services
- [Amazon Kinesis Data Streams](https://aws.amazon.com/kinesis/data-streams/)
- [Amazon Kinesis Data Analytics](https://aws.amazon.com/kinesis/data-analytics/)
- [AWS Lambda](https://aws.amazon.com/lambda/)

### Other
- [Kinesis Producer Library](https://github.com/awslabs/amazon-kinesis-producer)
- [Amazon Kinesis Data Analytics Java Examples](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples)
- [Flink: Hands-on Training](https://ci.apache.org/projects/flink/flink-docs-master/learn-flink/)
- [Streaming Analytics Workshop](https://streaming-analytics.workshop.aws/flink-on-kda/)
- [Kinesis Scaling Utility](https://github.com/awslabs/amazon-kinesis-scaling-utils)

