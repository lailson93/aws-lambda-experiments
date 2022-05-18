FROM node:alpine

WORKDIR /usr/src/app

RUN npm install -g serverless@2.44.0 && \
  npm install --save-dev serverless-localstack

# sls deploy --verbose -s $STAGE

CMD ["tail", "-f", "/dev/null"]