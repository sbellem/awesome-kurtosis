FROM node:lts-iron

RUN apt-get update && apt-get install --yes --no-install-recommends \
        iputils-ping \
        vim \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/

COPY args.yaml hardhat.config.ts package.json tsconfig.json .

RUN yarn

COPY contracts contracts
COPY scripts scripts
COPY test test

RUN npx hardhat compile
