# BUILD STAGE
FROM docker-hub.repo.viacom.com/node:20-alpine AS build-stage
LABEL stage=build-stage

WORKDIR /tmp/server
COPY package.json yarn.lock ./
COPY src/client/package.json src/client/yarn.lock ./src/client/
RUN yarn install --frozen-lockfile

COPY . ./
RUN yarn && yarn build

# PROD STAGE
FROM docker-hub.repo.viacom.com/node:20-alpine AS prod-stage
RUN apk update && apk upgrade --no-cache
RUN apk add procps

WORKDIR /apps/nf-data-explorer

COPY --from=build-stage /tmp/server/dist ./dist
COPY --from=build-stage /tmp/server/node_modules ./node_modules
COPY --from=build-stage /tmp/server/package.json ./package.json
COPY --from=build-stage /tmp/server/schema/discovery-schema.json ./schema/discovery-schema.json

CMD ["yarn", "start"]

