# serverless-ws
Serverless workshop with AWS Lambda, GraalVM and Micronaut

## Step by step guide

### Prerequisite check

1. Make sure you have the following prerequisites installed on your device:
- Micronaut
- AWS Cli
- Docker
- Java 11
- Intellij or your prefered IDE
- Maven

### Login AWS cli

2. Run the command on the terminal `aws configure` and use the credentials provided by the Perficient team, make sure you make your default region as us-east-1. By doing this, you will be able to xreate resources from the CLI.

For any resource created during this workshop, make sure to have as name prefix "workshop".

### Create micronaut project

3. Using the terminal, go to the folder where you will be working and run the following commands:


`mn create-function-app com.perficient.fn-one --features=graalvm,aws-lambda --build=maven --lang=java`


This will create a `Hello world` project ready to be used in aws lambda.

### Create native image

4. Once you have reviewed the project, to deploy to AWS Lambda as a GraalVM native image, run:

```bash
./mvnw package -Dpackaging=docker-native -Dmicronaut.runtime=lambda -Pgraalvm
```

This will build the GraalVM native image inside a docker container and generate the `function.zip` ready for the deployment.

### Deploy function to AWS Lambda

5. Once you have the zip folder with the function you need to create it in AWS using the following command:

`aws lambda create-function --function-name workshop-fn-one-micronaut --runtime provided.al2 --zip-file fileb://function.zip --handler com.perficient.FunctionRequestHandler --role "arn:aws:iam::{account_number}:role/lambda-fn-one-role"`

Now you are able to test your lambda function throug the AWS UI, you can access it using the [training console link](https://psl-training.signin.aws.amazon.com/console)

Try testing the function with the following payload

```bash
{
	"path": "/",
	"httpMethod": "GET",
	"headers": {
		"Accept": "application/json"
	}
}
```


### Create an API Gateway

6. Now you need to create an API Gateway to be able to call the lambda function using a public URL. Run the following command to create the gateway:

`aws apigateway create-rest-api --name 'workshop-apigateway' --region us-east-1`

Make sure to copy the API Gateway identifier once created.

### Create a resource to access the lambda function

7. Using the AWS API Gateway UI, create a resource in the gateway you created an link the lambda function created in step 5.

### Define a deployment stage for the API Gateway

8. Under the api gateway cteated, go to resources > actions > deploy api and define a deployment stage.

### Define a usage plan for your API

9. Under API Gateway > Usage plans, click on create button and define a usage plan with value 1000 for Rate, Brust and Quota. Click on next and add the API and stage created.

### Create an API key

10. Under API Gateway > API Keys, create an apikey.

### Assign the Key to the usage plan

11. Under API Gateway > Usage plans, select the usage plan you created. Go to the API keys tab and click on Add API key to usage plan, type the name of the key created.

### Deploy the API Gateway

12. Under the api gateway cteated, go to resources > actions > deploy api and select the deployment stage created, then click deploy.

### Access the API gateway

13. Under the api gateway cteated, go to stages section and select the stage defined. The UI will show you a invoke URL, use it to access the API Gateway. Make sure to add your api key as header `x-api-key` for the request to work.

