# Serverless-ws
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

2. Run the command on the terminal `aws configure` and use the credentials *provided by the Perficient team*
Make sure you make your default region as *us-east-1*. By doing this, you will be able to create resources from the CLI.

```bash
aws configure

AWS Access Key ID  [YOUR_ACCESS_KEY]
AWS Secret Access Key  [YOUR_SECRET_KEY] 
Default region name [us-east-1]
Default output format [json]
```

### Create micronaut project

**_NOTE:_** For any resource created during this workshop, make sure to have as name prefix *"workshop"*.

3. Using the terminal, go to the folder where you want to generate the project and run the following commands:

`mn create-function-app com.perficient.workshop-clei-function --features=graalvm,aws-lambda --build=maven --lang=java`

This will create a `Hello world` project ready to be used in aws lambda.

### Create native image

4. Once you have reviewed the project with your IDE, you can generate the native image, in the project root run:

**_NOTE:_** Make sure to have docker running.

From unix OS
`./mvnw package -Dpackaging=docker-native -Dmicronaut.runtime=lambda -Pgraalvm`

From Windows Powershell
`mvn package '-Dpackaging=docker-native' '-Dmicronaut.runtime=lambda' '-Pgraalvm'`

From Windows bash console
`mvn package -Dpackaging=docker-native -Dmicronaut.runtime=lambda -Pgraalvm`

From Intellij embedded maven menu
`Maven -> Eecute Maven Goal -> mvn package -Dpackaging=docker-native -Dmicronaut.runtime=lambda -Pgraalvm`

This will build the GraalVM native image **inside a docker container** and generate the `function.zip` in the target folder ready for the deployment.

### Login AWS portal

5.  You will need ACCOUNT_ID to deploy the lambda function in aws. You can get it by accesing the aws web UI using the [training console link]
(https://psl-training.signin.aws.amazon.com/console)

Use the credentials **provided by the Perficient team**

```bash
IAM user name = [YOUR_USER]
Password = [YOUR_PASSWORD]
```

Once logged in, AWS will ask you to change the password
You can use the same password adding an '*' at the end of the password

### Deploy function to AWS Lambda

6. In the project target folder you must have the native zipped function, you need to deploy it in AWS using the following command:

You have to run from **target** folder the next command

```bash
change [YOUR_NAME] by an specific identifier for your lambda
change [ACCOUNT_ID] and use the Account ID *without the middle dash(-)* from the portal menu in the user top right corner

aws lambda create-function --function-name workshop-clei-function-[YOUR_NAME] --runtime provided.al2 --zip-file fileb://target/function.zip --handler com.perficient.FunctionRequestHandler --role "arn:aws:iam::[ACCOUNT_ID]:role/lambda-fn-one-role"
```

### Testing Lambda from AWS Portal

7. Now you are able to test your lambda function using the AWS Portal. Try testing it, search for *Lambda* in the portal search bar.
There is the list of the AWS lambda functions
Enter in your lambda

Select the **Test** menu

Then click on **Test** button

The response body must be

```bash
{
  "statusCode": 200,
  "body": "{\"message\":\"Hello World\"}"
}
```

### Create an API Gateway

8. Now you need to create an API Gateway to be able to call the lambda function using a public URL. Run the following command to create the gateway:

```bash
change [YOUR_NAME] by an specific identifier for your API Gateway

aws apigateway create-rest-api --name workshop-clei-apigateway-[YOUR_NAME] --region us-east-1
```

### Create a method GET to access the lambda function

9. Using the AWS API Gateway UI, create the method GET in the gateway 

Enter in your API Gateway

`Resources > Actions > Create Method > In the drop down list select GET method`

Select the GET method

In the Lambda Function **autocomplete** search your lambda function

Save the change

### Testing Lambda from AWS API Gateway

10. Click on the GET method > click on *Test* option with the Thunder icon > click on Test button and the response must be

```bash
{"statusCode":200,"body":"{\"message\":\"Hello World\"}"}
```

### Define a deployment stage for the API Gateway

11. Under the api gateway created, go to resources > actions > deploy api > deployment stage

Create a new deployment stage

Stage name = dev

Click on deploy button

### Define a usage plan for your API

12. Under API Gateway > Usage plans > create 

```bash
Name = workshop-clei-usage-plan-[YOUR_NAME]

Throttling rate = 100
Burst rate = 100
Quota rate = 100
```
define a usage plan with value 100 for Rate, Brust and Quota. Click on next and add the API and stage created.

**Add API Stages**

In API select in the drop down menu your api gate way
In Stage select dev


**Create API Key and add to usage plan**
 Name = workshop-clei-api-key-[YOUR_NAME]
 
 Then click "Done"
 
### Require API key on a method

13. The following procedure describes how to configure an API method to require an API key.

- In the API Gateway main navigation pane, choose Resources.
- Under Resources, create a new method or choose an existing one.
- Choose Method Request.
- Under the Settings section, choose true for API Key Required.
- Select the checkmark icon to save the settings.

### Re-Deploy the API Gateway

14. Under the api gateway created, go to resources > actions > deploy api and select the deployment stage *dev*, then click deploy.

### Access the API gateway

15. Under the api gateway created, go to stages section and select the stage defined. The UI will show you a invoke URL, use it to access the API Gateway. Make sure to add your api key as header `x-api-key` for the request to work.

