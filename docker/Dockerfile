# ---------------
# Install Dependencies
# ---------------
FROM node:16-alpine as builder

RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh

WORKDIR /opt

RUN git clone https://github.com/Ride-The-Lightning/RTL.git \
    && cd RTL \
    && git checkout v0.13.1

# ---------------
# Build App
# ---------------
RUN cd RTL \
    && npm install \
    && npm run buildfrontend \
    && npm run buildbackend \
    && npm prune --production

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