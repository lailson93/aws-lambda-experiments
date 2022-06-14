# laboratorio lambda com localstack

## 1. Descricao

A presente stack docker-compose e formada por dois servicos:
- Localstack
- Lab

### 1.1 Pre-requisitos

- Docker (version: 19.03.8)
- Docker Compose (version 1.25.5)

## 2. Lab. 1 - Localstack

### Inicializar servicos da stack docker-compose

- start localstack

```sh
docker-compose up -d
```

- acessando container lab

```sh
docker exec -it aws-lambda sh
```

## 3. Lab. 2 - Amazon Lambda com Serverless Framework

### 3.1 Exemplos

#### 3.1.1 [Exemplo 1]: API Gateway - Hello World

O exemplo encontra-se na raiz. Trata-se de um lambda acionado via requisicao a seu endpoint. A execucao desse exemplo utiliza os seguintes recursos AWS:
- Amazon Lambda
- CloudFormation
- S3
- CloudWatch
- IAM
- API Gateway

Deploy da lambda usando sls framework.

Dentro do container/servico `aws-lambda` execute os comandos a seguir:

```sh
sls deploy --verbose --stage local
```

Acionando o lambda:
```sh
curl http://endpoint/path
```

Caso seja necessario alguma alteracao no codigo da funcao, e consequentemten o seu redeploy, basta executar o `sls deploy` novamente. Dessa forma, um update sera realizado atraves da stack do `CloudFormation`.

### 3.1.2 [Exemplo 2]: Utilizando S3 como event

Este exemplo encontra-se dentro do container `aws-lambda` no seguinte `path` `/usr/src/app/examples-master/aws-golang-s3-file-replicator`. Como trata-se de um teste local utilizando `Localstack`, algumas alteracoes deverao ser feitas no arquivo de configuracao `serverless.yaml`, sao elas:

- Adicao do plugin Localstack
- Adicao de profile e stage em provider
- Adicao de variaveis customizadas

```yaml

...
plugins:
  - serverless-localstack

provider:
  ...
  profile: ${opt:profile, self:custom.profile}
  stage: ${opt:stage, self:custom.defaultStage}
  ...

custom:
  defaultStage: local
  profile: default
  localstack:
    debug: true
    stages: [local]  
```

Alem disso, como trata-se de uma funcao desenvolvida em Golang, sera necessario o build da mesma anterior ao seu deploy. Essas informacoes podem ser encontradas dentro do arquivo `Makefile` contido no diretorio do exemplo. Segue:

```sh
export GO111MODULE=on
env GOARCH=amd64 GOOS=linux go build -ldflags="-s -w" -o bin/replicator src/main.go
```

Em seguida basta realizr o deploy:
```sh
sls deploy --verbose --stage local
```

Acionando a lambda via adicao de arquivos em bucket:

```sh
awslocal s3 cp text.txt s3://replicator-input-101/text.txt
```

Uma  melhor visualizacao dos logs pode ser visualiazado na interface grafica do localstack atraves do servico `CloudWatch`.


### 3.1.3 [Exemplo 3]: S3 event trigger

Este exemplo esta contido dentro do diretorio `s3-event-trigger` e trata-se de mais uma funcao acionada via eventos em um determinado bucket do S3.

## x. Informacoes adicionais

```sh
pip install awscli-local
```

- create new bucket

```sh
awslocal s3 mb s3://mybucket
```

- aws config profile defaul

```sh
aws configure
# AWS_ACCESS_KEY_ID: test
# AWS_SECRET_ACCESS_KEY: test

```

- deploy lambda with sls framework

```sh
sls deploy --verbose --stage local
```

- listar lambdas

```sh
awslocal lambda list-functions
```

- listar apigateways

```sh
awslocal apigateway get-rest-apis
```

- listar cloudformation stacks

```sh
awslocal cloudformation list-stacks
awslocal cloudformation delete-stack --stack-name app-local
```

- invoke functions with sls

```sh
sls info
sls invoke -f functionName --stage local
```

```
docker-compose up -d
docker exec -it aws-lambda sh
https://app.localstack.cloud/dashboard
```

Adicao de arquivos em bucket via cli:

```sh
awslocal s3 cp text.txt s3://replicator-input-101/text.txt
```