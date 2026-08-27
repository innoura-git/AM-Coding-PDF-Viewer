FROM node:20 AS builder

RUN npm install -g gulp-cli@2.3.0

WORKDIR /app

COPY package*.json ./

ENV PUPPETEER_SKIP_DOWNLOAD=true

RUN npm install

COPY . .

# Debug: verify gulpfile exists
RUN ls -la
RUN npx gulp --version

RUN npx gulp generic


FROM node:20-slim AS runner

WORKDIR /app

RUN npm install -g http-server

COPY --from=builder /app/build/generic ./build/generic

EXPOSE 8888

CMD ["http-server", "build/generic", "-p", "8888", "-c-1", "--mime-type", "application/javascript:.mjs"]