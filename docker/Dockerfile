# ---------------
# Install Dependencies
# ---------------
FROM node:16-alpine as builder

RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh

WORKDIR /opt

RUN git clone https://github.com/Ride-The-Lightning/RTL.git && cd RTL && git checkout cln-peer-swap


RUN npm install

# ---------------
# Build App
# ---------------
COPY . .

# Build the Angular application
RUN npm run buildfrontend

# Build the Backend from typescript server
RUN npm run buildbackend

# Remove non production necessary modules
RUN npm prune --production

# ---------------
# Release App
# ---------------
FROM node:16-alpine as runner

WORKDIR /RTL

RUN apk add --no-cache tini

COPY --from=builder /opt/RTL/rtl.js ./rtl.js
COPY --from=builder /opt/RTL/package.json ./package.json
COPY --from=builder /opt/RTL/frontend ./frontend
COPY --from=builder /opt/RTL/backend ./backend
COPY --from=builder /opt/RTL/node_modules/ ./node_modules

EXPOSE 3000

ENTRYPOINT ["/sbin/tini", "-g", "--"]

CMD ["node", "rtl"]