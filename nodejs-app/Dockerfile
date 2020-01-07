FROM alpine:3.10  AS base
RUN apk --no-cache add nodejs=10.16.3-r0 nodejs-npm=10.16.3-r0  --virtual native-deps \
  g++=8.3.0-r0 \
  gcc=8.3.0-r0 \
  libgcc=8.3.0-r0 \
  libstdc++=8.3.0-r0 \
  linux-headers=4.19.36-r0 \
  autoconf=2.69-r2 \
  automake=1.16.1-r0 \
  make=4.2.1-r2 \
  nasm=2.14.02-r0 \
  python=2.7.16-r1 \
  git=2.22.2-r0 && \
  npm install --quiet node-gyp@3.7.0  
WORKDIR /parse
COPY ./package.json .
ENTRYPOINT ["/sbin/tini", "--"]


FROM base AS dependencies
RUN npm set progress=false && npm config set depth 0
RUN npm install --only=production && npm audit fix



FROM dependencies AS test
COPY . .
RUN  npm run lint


FROM node:10.16.3-alpine  AS release
WORKDIR /node-app
RUN chown -R node:node /node-app
COPY --from=dependencies /parse/node_modules  ./node_modules
COPY . .
EXPOSE 1337
USER node
CMD [ "npm", "start" ]
