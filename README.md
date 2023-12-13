# Marketplace BAP
This service acts as a Seeker platform which fetches the course catalogs from the different provider platforms (BPPs) for the marketplace application. 

This doc underlines the architectural implementational details of reference **Beckn Application Platform (BAP)** implementation for the **Learning Experiences** track complaint to **DSEP spec v1.1.0.**


### Deployment and Network Registration Details

The reference BAP implemented in this repository is onboarded on the `Onest Gateway` and `Onest Registry` under the `Learning Experiences` category.

<!-- - [**BPP Deployed URL**](https://bpp.dsep.samagra.io) -->
- [**BAP Deployed URL**](https://test1-bap-compass-samagra.loca.lt)
<!-- - **BPP Network Participant Id**: bpp.dsep.samagra.io -->
<!-- - [Network Participant Information on Onest Registry](https://registry.becknprotocol.io/network_participants/index/network_participants/show/430) -->

## Tech Stack

**Programming Language**: TypeScript
**Framework**: NestJS
**Runtime**: Node18

## Project Organisation

This reference implementation contains all three network participants, i.e. **Beckn Provider Platform**, **Beckn Gateway**, **Beckn Provider Platform**, organised as a **NestJS Monorepo**.

### Directory structure

```bash
.
├── apps
│   ├── bap
│   ├── bg
│   └── bpp
├── docs
│   └── examples
├── tsconfig.build.json
├── tsconfig.json
├── types
│   └── schema.ts
├── utils
│   ├── generators.ts
│   ├── types.ts
│   └── utils.ts
├── nest-cli.json
├── package.json
├── README.md
└── yarn.lock
```

The `apps` directory contains the actual apps for each network participant
`apps/bap` and `apps/bg` contains the code for a sample `Beckn Application Platform` and `Beckn Gateway` respectively for testing the `Beckn Provider Platform`.

<!-- More details about each network participant and their implementation can be found their following directories here as:

- [Beckn Application Platform (BAP)](https://github.com/Samagra-Development/dsep/blob/master/apps/bap/README.md)
- [Beckn Gateway(BG)](https://github.com/Samagra-Development/dsep/blob/master/apps/bg/README.md)
- [Beckn Provider Platform (BPP)](https://github.com/Samagra-Development/dsep/blob/master/apps/bpp/README.md) -->

<!-- ## Architecture

![Block Diagram](./docs/Arch.png) -->

## Features & User workflow

The reference app is a **Course Seeker Platform** that talks in **Decentralised Skilling and Education Protocol** and curates courses from various providers. Right now only a mock BPP is onboarded as a provider, which sends mock responses for every request.

The `/search` request is forwarded to the `Onest Gateway` with the domain of `onest:learning-experiences` so that reaches our reference `BPP`. The `BPP` then responds with a mock search response containing course catalog as search responses in beckn `/on_search` compliant form.  The `BPP` then sends the search responses to the `/on_search` endpoint of our service. Then the search responses are aggregated in the redis store as value for every search request with key as `messageId`. This enables the marketplace-portal service to poll for search responses until a maximum threshold time as the responses from different `BPPs` come async.


### Supported Methods

**/search:** This method/endpoint allows for searching of courses and training via a direct DSEP complaint request to the BPP using the context.domain as `onest:learning-experiences`

**/confirm**: This method/endpoint indicated the confirmation of a course purchase order after successful payment/enrollment. In the reference course discovery platform implementation this endpoint is called when visiting the course on the external website.

**/rating**: This method/endpoint allows for providing ratings for different courses that were completed by users.

## Local installation

Follow the following steps to setup the monorepo locally on your system.

1. Clone the repository

    ```bash
    git clone https://github.com/Samagra-Development/dsep
    ```

2. Navigate into the directory where you have cloned the repository

```bash
cd /path/to/cloned/repository
```

3. Install the required dependencies using the package manager of your choice (yarn preferred).

    ```bash
    yarn install
    ```

4. Create a `.env` file similar to the `.env,sample` file and populate it with required credentials
5. [Install Docker](https://docs.docker.com/engine/install/), [Install Docker Compose](https://docs.docker.com/compose/install/linux/), and run

    ```bash
    docker compose up
    ```

6. Navigate to your hasura UI which will be started after running the command in `Step 5` and create a table named `dsep_courses`.
7. Run the services for the required network participant using the following commands

    ```bash
    yarn start <bap | bg | bpp> # replace <bap | bg | bpp> with a single name
    # for example: yarn start bpp will start the BPP
    ```

Or start all of them together using
    ```bash
    yarn start:all # this will start all the services
    ```

## Deployment

1. [Install Docker](https://docs.docker.com/engine/install/), [Install Docker Compose](https://docs.docker.com/compose/install/linux/), and run

    ```bash
    docker compose up
    ```

2. PM2 Based Deployment

    ```bash
    yarn build
    pm2 start dist/apps/bpp/main.js --name beckn-bpp
    pm2 start dist/apps/bap/main.js --name beckn-bap
    pm2 start dist/apps/bg/main.js --name beckn-bg
    ```

## Related Repositories

- [Mock Provider (Swayam)](https://github.com/Samagra-Development/swayam-wrapper)
- [BAP Client Proxy](https://github.com/Samagra-Development/dsep-ui/tree/master/apps/client-proxy) - Acts as a service connecting Client and Proxy
- [BAP Client](https://github.com/Samagra-Development/dsep-ui) - A course search page

## Adding Services to Beckn Registry

Follow [this guide](https://github.com/sanjay95/BECKN-Integration-to-Gateway/blob/main/README.md) to onboard yourself on the Beckn Registry.

## Resources to know more about DSEP, ONEST and Beckn

- [Beckn Official Website](https://becknprotocol.io)
- [Core DSEP Specification](https://github.com/beckn/protocol-server/blob/v2/schemas/core.yaml)
- [DSEP use case tracks discussion](https://github.com/beckn/DSEP-Specification/discussions)
- [ONEST Starter Pack](https://starterpack.onest.network/learn/introduction)
- [ONEST Sandbox network](https://sandbox.onest.network/)

