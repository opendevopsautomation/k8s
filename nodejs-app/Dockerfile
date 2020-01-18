FROM node:10.16.3-alpine  AS base
RUN npm install eslint@6.8.0 -g
WORKDIR /parse
COPY ./package.json .
RUN npm set progress=false && npm config set depth 0
RUN npm install --only=production && npm audit fix

FROM base AS test
COPY . .
RUN  npm run lint

FROM node:10.16.3-alpine  AS release
WORKDIR /node-app
RUN chown -R node:node /node-app
COPY --from=base /parse/node_modules  ./node_modules
COPY . .
RUN rm -f .eslintrc.json
USER node
EXPOSE 1337
CMD [ "npm", "start" ]
