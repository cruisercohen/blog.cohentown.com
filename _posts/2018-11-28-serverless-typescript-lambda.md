---
layout: post
title: AWS Serverless API with the Serverless Framework, node, and Typescript
author: Cruiser
---

I've been working on some new serverless projects and it's starting to come together. I'm using the serverless.com framework, AWS Lambda and API Gateway, and node with typescript for my Lambda functions. The typescript types for the Lambda functions as API Gateway Proxies is a bit tricky at first, but it's really helpful.

# Basic Setup

We're going to create a new project and get it setup to run serverless locally and deploy to AWS  

## AWS Keys
First configure your key and secret in AWS and keep those handy. You can get a new key and secret from the AWS Console in IAM if you don't have one already.  

## Project Setup Commands
Now let's run some commands to setup the project
```bash
mkdir so-serverless && cd so-serverless
npm i serverless -g  #(or serverless can be installed locally to this project and run with npx. npm i serverless --save-dev)  
sls create --template aws-nodejs
```

Now that we have serverless installed let's setup your AWS keys, this will allow serverless to deploy to your account.

```bash
sls config credentials --provider aws --key <your key> --secret <your super secret secret>
```

Next we setup npm and install some usefull libraries:
* **serverless-offline** allows us to run our API Gateway endpoints and Lambda function locally  
* **serverless-plugin-typescript** helps run our tsc compiler when we run locally or deploy
* **@types/node** and **@types/aws-lambda** give us all the types we need to work with node Lambdas in Typescript. These are both from DefinitelyTyped which is super useful for Typsescript work.  

```bash
npm init
tsc --init
npm i serverless-offline serverless-plugin-typescript --save-dev  
npm i @types/node @types/aws-lambda --save-dev
```

## Serverless Framework Setup

Configure `serverless.yml`  

Setup the provider, your functions and the API gateway, which is the http event on each function. Also at the top level of `serverless.yml`

```yml
service: so-serverless 

provider:
  name: aws
  runtime: nodejs8.10

  region: us-west-2 # defaults to us-east-1

functions:
  hello:
    handler: handler.hello

    events:
      - http:
          path: hello
          method: get
```
Also add a new section anywhere at the top level to include your plugins

```yml
plugins:
  - serverless-plugin-typescript
  - serverless-offline
```

## Sample Function

Replace the default `handler.js` file with `handler.ts` and add the following to the file. This is our basic Lambda Proxy function for our API Gateway route. Notice all the types we're using from `@types/aws-lambda`. This really makes it easy to ensure you are correctly handling the input from an API Gateway request and returning the correct type back in response.  
NOTE: The Handler must be exported as a normal node module export. Lambda does not support functions in classes as the entry point.

```ts
import { Handler, Context, APIGatewayProxyResult, APIGatewayProxyEvent } from 'aws-lambda';

export const hello: Handler = async function (event: APIGatewayProxyEvent, context: Context) : Promise<APIGatewayProxyResult> {
    const response: APIGatewayProxyResult = {
        statusCode: 200,
        body: JSON.stringify({
            message: 'typed serverless!'
        })
    };
    return response;
}
```

# Run It

Run locally
```bash
sls offline start
```

Or deploy and check it out running directly on AWS
```bash
sls deploy
```

Remove when done
```bash
sls remove
```

# Inspiration
Found a lot of inspiration for this, but there were some modifications I wanted to make to each so my result is a bit of a mashup and some customization.  

Great stuff here. My example uses async methods because I don't like callbacks, personal preference.  
<https://blog.shovonhasan.com/deploying-a-typescript-node-aws-lambda-function-with-serverless/>  

An example from the serverless framework samples library, it doesn't make use of explicit types and also does callbacks, but shows a fully setup project  
<https://github.com/serverless/examples/blob/master/aws-node-typescript-rest-api-with-dynamodb/todos/create.ts>

#### Aws documentation:
The upshot is you need to create a Lambda Function Handler that accepts and returns API Gateway events and results:

Documentation on the AWS Lambda function handler and how it should be formatted, which you will find in `@types/aws-lambda` anyway  
<https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html#nodejs-prog-model-handler-example>  
Documentation on AWS Lambda proxy integrations in API Gateway, shows correct formats, also in `@types/aws-lambda`  
<https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html>

