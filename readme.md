# Lab Documentation: Counter Application with AWS Services

## **Overview**

This lab demonstrates the creation of a web-based counter application using multiple AWS services. The application allows users to increment a counter, save the count value, and store it in DynamoDB through a chain of AWS services.

## **Architecture**

```
Browser -> API Gateway -> Lambda 1 (HTML) -> API Gateway -> Lambda 2 (DynamoDB) -> DynamoDB

```

![Alt Text](path/to/image(1).png "Optional Title")

![Alt Text](path/to/image(2).png "Optional Title")

## **Components Used**

1. API Gateway - For handling HTTP requests
2. Lambda Function 1 - Serves HTML content
3. Lambda Function 2 - Processes count and saves to DynamoDB
4. DynamoDB - Stores counter values
5. IAM Roles - For service permissions

## **Step-by-Step Implementation**

### 1. DynamoDB Table Setup

```
1. Created DynamoDB table:
   - Table name: counter_table
   - Partition key: id (String)
   - Additional attributes:
     - count (Number)
     - timestamp (String)

```

### 2. First Lambda Function (HTML Service)

```
# Function name: HTMLServiceFunction# Runtime: Python 3.9# Purpose: Serves the HTML page with counter functionalitydeflambda_handler(event, context):
    html = """
    <!DOCTYPE html>
    <html>
    <!-- HTML code with counter interface -->
    <script>
        async function saveCount() {
            try {
                const response = await fetch('https://tgj72sslcl.execute-api.us-east-1.amazonaws.com/prod/lab2', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        count: count,
                        timestamp: new Date().toISOString()
                    })
                });
                // Handle response...
            } catch (error) {
                // Handle error...
            }
        }
    </script>
    """
return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'text/html',
            'Access-Control-Allow-Origin': '*'
        },
        'body': html
    }

```

### 3. Second Lambda Function (DynamoDB Service)

```
# Function name: DynamoDBServiceFunction# Runtime: Python 3.9# Purpose: Processes count and saves to DynamoDBimport json
import boto3
import random 
from datetimeimport datetime

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('counter_table')

deflambda_handler(event, context):
try:
# Process incoming data and save to DynamoDB
        body = json.loads(event['body'])
        count = body['count']
        timestamp = datetime.now().isoformat()
				random_int = random.randint(1, 10)
# Save to DynamoDB
        item = {
            'id':str(random_int),
            'count': count,
            'timestamp': timestamp
        }
        table.put_item(Item=item)

return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
            'body': json.dumps({'message': 'Success'})
        }
except Exceptionas e:
return {
            'statusCode': 500,
            'body': json.dumps({'error':str(e)})
        }

```

![image.png](attachment:fc8d3789-92a5-4182-940d-48ebecef0847:image.png)

### 4. API Gateway Configuration

```
1. Created REST API:
   - Name: CounterAPI
   - Protocol: REST
   - EndpointType: Regional

2. Created Resources:
   a. First Resource (HTML Service):
      -Method: GET
      - IntegrationType: LambdaFunction
      -LambdaFunction: HTMLServiceFunction

   b. Second Resource (Counter Save):
      - Path: /lab2
      -Method: POST
      - IntegrationType: LambdaFunction
      -LambdaFunction: DynamoDBServiceFunction

3. CORS Configuration:
   - Enabled CORSfor both resources
   - Allowed Headers: Content-Type
   - Allowed Methods: GET, POST
   - Allow Origin: *

4. Deployed API:
   - Stage name: prod
   - Generated Invoke URL

```

![image.png](attachment:649b4b0f-7f6e-404b-a526-3a61e138ae80:image.png)

### 5. IAM Role Configurations

```
1. HTML Lambda Role:
   - Basic Lambda execution permissions
   - CloudWatch Logs access

2. DynamoDB Lambda Role:
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/counter_table"
        }
    ]
}

```

### 6. Testing Procedures

```
1. HTML Function Testing:
   - Test event: {"httpMethod": "GET"}
   - Expected: Returns HTML content

2. DynamoDB Function Testing:
   - Test event: {
       "body": {
           "count": 5,
           "timestamp": "2025-09-20T01:00:00Z"
       }
   }
   - Expected: Saves data to DynamoDB

3. End-to-End Testing:
   - Open API Gateway URL in browser
   - Click counter button
   - Click save
   - Verify in DynamoDB console

```

### 7. Troubleshooting

Common issues encountered and solutions:

1. CORS errors: Added appropriate CORS headers
2. Lambda timeout: Increased timeout duration
3. DynamoDB permissions: Updated IAM roles
4. API Gateway integration: Verified endpoint configurations

## **Conclusion**

This lab demonstrates the integration of multiple AWS services to create a functional web application. The architecture shows how to:

- Serve dynamic HTML content through Lambda
- Handle client-side interactions
- Process data through multiple Lambda functions
- Store data in DynamoDB
- Configure proper service permissions
- Handle cross-origin requests

## **Future Improvements**

Potential enhancements:

1. Add user authentication
2. Implement data retrieval functionality
3. Add error handling and retries
4. Implement data validation
5. Add monitoring and alerts

Would you like me to expand on any section of this documentation?
