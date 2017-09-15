# Dockerfile building custom elasticsearch image
# The target image is used for deployment on Kubernetes
# Solves the issue presneted in: https://github.com/kubernetes/kubernetes/issues/3595
# Applies the solution presented in: 
# https://github.com/kubernetes/kubernetes/issues/3595#issuecomment-287692878 [Kubernetes Side]
# https://github.com/kubernetes/kubernetes/issues/3595#issuecomment-288451522 [This image]
# This image is being extended: 
# ------------------------------
# https://github.com/elastic/elasticsearch-docker/blob/master/templates/Dockerfile.j2


FROM docker.elastic.co/elasticsearch/elasticsearch:5.6.0

WORKDIR /usr/share/elasticsearch

USER root

# copying custom-entrypoint and configuration (elasticsearch.yml, log4j2.properties)
# to their respective directories in /usr/share/elasticsearch (already the WORKDIR)
COPY custom-entrypoint bin/
COPY elasticsearch.yml config/
COPY log4j2.properties config/

# assuring "elasticsearch" user have appropriate access to configuration and custom-entrypoint
# make sure custom-entrypoint is executable
RUN chown elasticsearch:elasticsearch config/elasticsearch.yml config/log4j2.properties bin/custom-entrypoint && \
    chmod 0750 bin/custom-entrypoint

# start by running the custom entrypoint (as root)
CMD ["/bin/bash", "bin/custom-entrypoint"]
