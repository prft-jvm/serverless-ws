# serverless-ws
Serverless workshop with AWS Lambda, GraalVM and Micronaut

##Step by step guide
1. Make sure you have the following prerequisites installed on your device:
- Micronaut
- AWS Cli
- Docker
- Java 11
- Intellij or your prefered IDE
- Maven
2. Run the command on the terminal `aws configure` and use the credentials provided by the Perficient team, make sure you make your default region as us-east-1. By doing this, you will be able to xreate resources from the CLI.
3. Using the terminal, go to the folder where you will be working and run the following commands:


`mn create-function-app com.perficient.fn-one --features=graalvm,aws-lambda --build=maven --lang=java`


This will create a `Hello world` project ready to be deployed in aws lambda.

4. Once you have reviewed the project execute the following command to build the function to be deployed:

`mvn package -Dpackaging=docker-native -Dmicronaut.runtime=lambda -Pgraalvm`

It will take some minutes to finish the process and as a result you will have in the target folder a zip file called function.zip.

5. Once you have the zip folder with the function you need to create it in AWS using the following command:

`aws lambda create-function --function-name workshop-fn-one-micronaut --runtime provided.al2 --zip-file fileb://function.zip --handler com.perficient.FunctionRequestHandler --role "arn:aws:iam::{account_number}:role/lambda-fn-one-role"`

Now you are able to test your lambda function throug the AWS UI, you can access it using the [training console link](https://psl-training.signin.aws.amazon.com/console)

