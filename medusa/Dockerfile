FROM node:20 as builder

WORKDIR /app/medusa

COPY . . 

RUN rm -rf node_modules

RUN apt-get update && apt-get install -y python3 python3-pip python-is-python3

ENV NODE_OPTIONS="--dns-result-order=ipv4first"

#RUN yarn install --frozen-lockfile --network-timeout 600000

RUN yarn config set network-timeout 600000 -g \
 && yarn config set enableIPv6 false \
 && yarn config set registry https://registry.npmjs.org \
 && yarn install --frozen-lockfile --network-timeout 600000 --mutex network \
 || yarn install --network-timeout 600000 --mutex network

RUN yarn run build


FROM node:20

# Bug https://github.com/coollabsio/coolify/issues/1930
ARG COOKIE_SECRET
ARG JWT_SECRET
ARG STORE_CORS
ARG ADMIN_CORS
ARG AUTH_CORS
ARG DISABLE_ADMIN
ARG WORKER_MODE
ARG PORT
ARG DATABASE_URL
ARG REDIS_URL
ARG BACKEND_URL

RUN echo "${PORT}"

WORKDIR /app/medusa

RUN mkdir .medusa

# COPY package*.json .yarnrc.yml yarn.lock ./ 

#COPY medusa-config.ts .
#COPY tsconfig.json .
#COPY package.json .

# COPY medusa-config.ts .
# COPY tsconfig.json .

RUN apt-get update && apt-get install -y python3 python3-pip python-is-python3

COPY --from=builder /app/medusa/.medusa ./.medusa

WORKDIR /app/medusa/.medusa/server

ENV NODE_OPTIONS="--dns-result-order=ipv4first"

RUN yarn install --production --network-timeout 600000


#RUN medusa migrations run 
#COPY migrations.sh /app/medusa/.medusa/server/migrations.sh
#RUN chmod +x /app/medusa/.medusa/server/migrations.sh
#RUN /app/medusa/.medusa/server/migrations.sh

#RUN rm /app/medusa/.medusa/server/migrations.sh

#CMD ["sh", "-c", "npx medusa db:migrate && yarn run start"]
#CMD ["yarn", "run", "start"]
#CMD ["yarn", "start"]
#CMD ["npx", "medusa", "db:migrate"]
#CMD ["sh", "-c", "cd /app/medusa/.medusa/server && npx medusa db:migrate"]
CMD ["sh", "-c", "cd /app/medusa/.medusa/server && npx medusa db:migrate && npx medusa start"]
