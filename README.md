## 新規環境面の構築手順

- 本パッケージでは、全AWSリソースをCloudFormationのスタックとして管理しているため、環境構築はCloudFormationのみで実施できる。

### 01.IAM

#### 2.lambda_iam.yml
- LambdaがDynamoDBテーブルにアクセス可能となるためのポリシーおよびロールを作成。
- **全環境共通のため、初回のみ流す。**

### 02.Cognito

#### 1.cognito-user-pool.yml
- Cognitoのユーザプールとアプリクライアントを作成する。
- パラメータとして環境名を選択し、流す。
Cognitoマネジメントコンソールから、作成が成功したことを確認する。

#### 2.cognito-identity-pool.yml
- 1.で作成したユーザプールにひもづくアイデンティティプールを作成する。
- パラメータとして環境名を選択し、流す。
- Cognitoマネジメントコンソールから、作成が成功したことを確認する。

#### 3.cognito-user-group.yml
- 1.で作成したユーザプールのユーザグループを作成する。
- Cognitoマネジメントコンソールから、下記のグループが作成されたことを確認する。
    - Administrator
    - Owner
    - StockManager
    - SalesRecorder

### 03.Middleware

#### ftf-web-management-dynamodb.yml
- DynamoDBテーブルを作成する。
- 下記テーブルが作成されたことを確認する。
    - FOOD_{環境名}
    - WHOLESALER_{環境名}
    - STOCK_{環境名}
    - FLOW_{環境名}
    - SHOP_{環境名}
    - BUSINESS_DATE_{環境名}
    - HOLIDAY_{環境名}
    - SEQUENCE_{環境名}

### 04.API Gateway

#### 1.lambda.yml
- 各Lambdaをデプロイする。
- `ftf-web-lambda`のREADMEを参考にデプロイする。

#### 2.api-gateway.yml
- API Gatewayをデプロイする。
- スタック作成後に、API GatewayマネジメントコンソールからAPIのデプロイを選択し、完了。

### 05.CodePipeline
#### code-pipeline.yml
- 画面モジュールをデプロイするためのCodePipelineを作成する。
- 環境名、デプロイ先S3バケット名を正しく選択し、スタックを作成する。

### 06.CloudFront
#### cloudfront.yml
- 独自ドメインでアクセス可能とするためのCloudFrontディとリビューションを作成する。
- 環境名を正しく選択し、スタックを作成する。
