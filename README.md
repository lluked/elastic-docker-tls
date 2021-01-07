# About
- Based on https://www.elastic.co/guide/en/elastic-stack-get-started/7.10/get-started-docker.html#get-started-docker-tls
- All config is however within standard yml files instead of being passed through as docker environment variables in order to simulate production with physical servers.
# Setup
## Create certs
docker-compose -f create-certs.yml run --rm create_certs
## Start stack
docker-compose -f elastic-docker-tls.yml up -d
## Generate passwords and output to file
docker exec es01 /bin/bash -c "bin/elasticsearch-setup-passwords auto --batch --url https://es01:9200" > PW.txt
## Check connection to es01 with elastic user (optional)
- elasticpw=PASSWORD
- docker run --rm -v es_certs:/certs --network=es_elastic docker.elastic.co/elasticsearch/elasticsearch:7.10.1 curl --cacert /certs/ca/ca.crt -u elastic:$elasticpw https://es01:9200
## Check connection to es01 with kinana_system user (optional)
- kibanapw=PASSWORD
- docker run --rm -v es_certs:/certs --network=es_elastic docker.elastic.co/elasticsearch/elasticsearch:7.10.1 curl --cacert /certs/ca/ca.crt -u kibana_system:$kibanapw https://es01:9200
## Put kibana password in kibana-kib01.yml file.
you get the picture
## Restart docker
docker-compose -f elastic-docker-tls.yml stop
docker-compose -f elastic-docker-tls.yml up -d
# Enjoy
The Elastic Stack is now setup with TLS and security features enabled for testing.
