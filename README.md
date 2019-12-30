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
- スタック作成後、出力にて下記項目の値をメモしておく。
    - FtfWebManagementAppClientId
    - FtfWebManagementUserPoolId
- Cognitoマネジメントコンソールから、作成が成功したことを確認する。

#### 2.cognito-identity-pool.yml
- 1.で作成したユーザプールにひもづくアイデンティティプールを作成する。
- パラメータとして環境名を選択し、流す。
- Cognitoマネジメントコンソールから、作成が成功したことを確認する。
- スタック作成後、出力にて下記項目の値をメモしておく。
    - CognitoIdentityPoolId

#### 3.cognito-user-group.yml
- 1.で作成したユーザプールのユーザグループを作成する。
- Cognitoマネジメントコンソールから、下記のグループが作成されたことを確認する。
    - Administrator
    - Developer
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
- 左サイドメニューのステージを選択肢、invocation urlをメモしておく。

### pkg-ftf-web-managementに新規環境面の設定ファイルを追加する
- [IT1面追加時のPR](https://github.com/neo0222/pkg-ftf-web-management/pull/14)を参考にしながら、新規環境面に対応できるよう修正を行う。

#### src/environment下のファイル設定
|項目名|値|
|---:|---|
|Region|ap-northeast-1|
|UserPoolId|FtfWebManagementUserPoolId(メモした値)|
|ClientId|FtfWebManagementAppClientId(メモした値)|
|IdentityPoolId|CognitoIdentityPoolId(メモした値)|
|InvokeUrl|API Gatewayデプロイ時にメモしたurl|

- PRを作成し、マージする。

### 05.CodePipeline
#### code-pipeline.yml
- 画面モジュールをデプロイするためのCodePipelineを作成する。
- 環境名、デプロイ先S3バケット名を正しく選択し、スタックを作成する。
- CodePipelineマネジメントコンソールから、デプロイが成功することを確認する。

### 06.CloudFront
#### cloudfront.yml
- 独自ドメインでアクセス可能とするためのCloudFrontディとリビューションを作成する。
- 環境名を正しく選択し、スタックを作成する。
