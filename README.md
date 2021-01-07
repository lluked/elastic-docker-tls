# About
- Based on https://www.elastic.co/guide/en/elastic-stack-get-started/7.10/get-started-docker.html#get-started-docker-tls
- All config is however within standard yml files instead of being passed through as docker environment variables in order to simulate production with physical servers.
# Setup
## Create certs
docker-compose -f create-certs/docker-compose.yml run -env=.env --rm create_certs
## Start stack
docker-compose up -d
## Generate passwords and output to file
docker exec es01 /bin/bash -c "bin/elasticsearch-setup-passwords auto --batch --url https://es01:9200" > PW.txt
## Put kibana_system password in .env file file.
ELASTICSEARCH_PASSWORD="PASSWORD_FROM_PW_FILE"
## Restart docker
docker-compose stop
docker-compose up -d
# Enjoy
- The Elastic Stack is now setup with TLS and security features enabled for testing.
- https://localhost:5601/
- user:password -> elastic:"PASSWORD_FROM_PW_FILE"
# Troubleshooting
## Check connection to es01 with elastic user
elasticpw="PASSWORD_FROM_PW_FILE"
docker run --rm -v es_certs:/certs --network=es_elastic docker.elastic.co/elasticsearch/elasticsearch:7.10.1 curl --cacert /certs/ca/ca.crt -u elastic:$elasticpw https://es01:9200
## Check connection to es01 with kinana_system user
kibanapw=PASSWORD_FROM_PW_FILE
docker run --rm -v es_certs:/certs --network=es_elastic docker.elastic.co/elasticsearch/elasticsearch:7.10.1 curl --cacert /certs/ca/ca.crt -u kibana_system:$kibanapw https://es01:9200
