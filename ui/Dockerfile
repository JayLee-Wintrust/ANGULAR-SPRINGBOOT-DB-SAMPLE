################################################################################
# Stage 1: Build Angular Application in Nodejs container
################################################################################
FROM registry.access.redhat.com/ubi9/nodejs-16@sha256:eea15f070005bd70d46fa67bf54dc5289883af75ddf98f2ea757e08ce9ef75ca as node-build-container

# ENV VAR needed during build for some older libraries (TODO: upgrade libraries)
ARG NODE_OPTIONS=--openssl-legacy-provider

# For conveience while developing in local but not in PROD
ARG NPM_CONFIG_STRICT_SSL=false

# Install dependencies in /usr/src/app
WORKDIR /usr/src/app
ADD --chown=1001:1001 package*.json ./
RUN npm ci

# Build angular assets into /usr/src/app/dist/${PACKAGE_JSON_NAME_VALUE}
ADD --chown=1001:1001 tsconfig*.json ./
ADD --chown=1001:1001 angular*.json ./
ADD --chown=1001:1001 src src
RUN npm run build


################################################################################
# Stage 2: Copy Angular Assets into Nginx container
################################################################################
FROM registry.access.redhat.com/ubi9/nginx-122@sha256:a0cbe950130cef3675bbe02dff7c9dd1186effc57e861ef9c4001964a6715f3c as nginx-container

# Application Name from package.json file
ARG PACKAGE_JSON_NAME_VALUE=employeemanagerapp

# Copy over angular assets from /usr/src/app/dist/${PACKAGE_JSON_NAME_VALUE} in the other container
COPY --from=node-build-container --chown=1001:1001 /usr/src/app/dist/${PACKAGE_JSON_NAME_VALUE} /opt/app-root/src

# Start Nginx
CMD nginx -g "daemon off;"
