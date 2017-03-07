# API Key w/ CloudFormation
#### 2017-03-06
Let's say you have an API Gateway that you would like to mildly protect so the world doesn't go nuts on your new API. A simple solution is to add an API key on that gateway so only you can get to it.

To add this to your APIG via CloudFormation, the documentation seems straightforward, but as with most AWS docs, seeing is not believing.

Currently there is a [bug](http://stackoverflow.com/questions/39992629/usageplankeys-cloudformation) (or missing feature, you decide) that doesn't allow an API key to be applied via CloudFormation, leaving it one step short. A UsagePlanKey is required to hook up the ApiKey to the UsagePlan, but it can't be done using CloudFormation!

The workaround is to complete that step in the AWS console once the stack has been updated, or use the REST API to complete that step.

A breakdown of how to get to this point:

1. Make sure you have a `AWS::ApiGateway::Stage` defined
2. Point your Stage to your API using the `RestApiId` property in the Stage defintion
3. Create a `AWS::ApiGateway::ApiKey` and add your RestApiId and StageName to the `StageKeys` property
4. Create a `AWS::ApiGateway::UsagePlan` and again add your API and Stage references to the `ApiStages` property
5. Finally, make sure your RestApi `body` Swagger contains a `securityDefinitions` block and a `security` block under your resource method that look like

        "securityDefinitions": {
          "x-api-key": {
            "type": "apiKey",
            "in": "header",
            "name": "x-api-key"
          }
        }
        ...
        "security":[
          {
            "x-api-key": []
          }
        ]
