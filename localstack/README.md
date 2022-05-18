# todo

- start localstack

```sh
docker-compose up -d
```

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
sls deploy --stage local
```

- listar lambdas

```sh
awslocal lambda list-functions
```

- invoke functions with sls

```sh
sls info
sls invoke -f functionName --stage local
```
