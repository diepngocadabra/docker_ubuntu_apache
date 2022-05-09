FROM nginx:latest
RUN apt-get update && apt-get install -y git openssl vim
# Change Nginx config here...
COPY conf.d ./etc/nginx/conf.d/
ARG BUILD_ENVIRONMENT
ARG GIT_USERNAME
ARG GIT_PASSWORD