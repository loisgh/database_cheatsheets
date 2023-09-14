### To create a dyanmodb table at the command line:
```
aws dynamodb create-table \
    --table-name YourTableName \
    --attribute-definitions \
        AttributeName=recordId,AttributeType=S \
    --key-schema \
        AttributeName=recordId,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=5,WriteCapacityUnits=5
```

##### The above table has a key of recordId and the key type is a Hash

### To add a record to the table:

```
aws dynamodb put-item \
    --table-name YourTableName \
    --item '{
        "recordId": {"S": "unique-record-id-1"},
        "name": {"S": "John Doe"},
        "age": {"N": "30"},
        "date_of_birth": {"S": "1993-01-15"},
        "email": {"S": "johndoe@example.com"}
    }'
```
### To update the record on the table:

```
aws dynamodb update-item \
    --table-name YourTableName \
    --key '{"recordId": {"S": "unique-record-id-1"}}' \
    --update-expression "SET email = :newEmail, age = :newAge" \
    --expression-attribute-values '{
        ":newEmail": {"S": "newemail@example.com"},
        ":newAge": {"N": "35"}
    }'
```
--key: Specifies the primary key of the item you want to update. In this case, it identifies the item using the recordId.

--update-expression: Defines the update operation to be performed. In this example, it uses the SET clause to set new values for the email and age attributes.

--expression-attribute-values: Specifies the values to be used in the update expression. In this case, it provides new values for email and age. Make sure to adjust the values and data types according to your requirements.

### To read the table by record id:

```
aws dynamodb get-item \
    --table-name YourTableName \
    --key '{"recordId": {"S": "unique-record-id-1"}}'
```
### To read the table by name:

```
aws dynamodb query \
    --table-name YourTableName \
    --index-name YourIndexName \
    --key-condition-expression "name = :nameValue" \
    --expression-attribute-values '{
        ":nameValue": {"S": "John Doe"}
    }
```

### SQL Queries

DynamoDB primarily uses its own query language and API for interacting with tables, rather than standard SQL. However, you can perform SQL-like queries on DynamoDB tables using AWS Glue DataBrew, Amazon Athena, or other AWS services that support SQL querying over data stored in DynamoDB. Below are examples using AWS Glue DataBrew and Amazon Athena.

**Example 1: Reading by RecordId using AWS Glue DataBrew:**

You can use AWS Glue DataBrew to create a dataset from your DynamoDB table and then perform SQL-like queries. Here's an example:

1. Create a DataBrew dataset for your DynamoDB table:
   - Sign in to the AWS Management Console.
   - Go to AWS Glue DataBrew.
   - Create a new project.
   - Add a dataset and choose "DynamoDB" as the source.
   - Configure the connection to your DynamoDB table.

2. Perform a query to read by RecordId:

```sql
SELECT *
FROM YourDatasetName
WHERE recordId = 'unique-record-id-1'
```

Replace `YourDatasetName` with the actual name of your DataBrew dataset and `'unique-record-id-1'` with the specific RecordId you want to query.

**Example 2: Reading by Name using Amazon Athena:**

Amazon Athena is a serverless query service that allows you to run SQL queries against data stored in Amazon S3, including data exported from DynamoDB.

1. Export your DynamoDB data to Amazon S3 using services like AWS Data Pipeline, AWS Glue, or custom code.

2. Create an Athena table that maps to your data in Amazon S3:

```sql
CREATE EXTERNAL TABLE YourAthenaTable (
  recordId STRING,
  name STRING,
  age INT,
  date_of_birth STRING,
  email STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
  'separatorChar' = ',',
  'quoteChar' = '"',
  'escapeChar' = '\\'
)
LOCATION 's3://your-s3-bucket/path-to-exported-data/'
```

3. Perform a query to read by Name:

```sql
SELECT *
FROM YourAthenaTable
WHERE name = 'John Doe'
```

Replace `YourAthenaTable` with the name of your Athena table and `'John Doe'` with the specific name you want to query.

Please note that these examples involve additional steps to export DynamoDB data to a format that can be queried using SQL-like syntax. DynamoDB itself does not support SQL queries natively.