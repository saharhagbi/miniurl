<p align="center">
    <img src="https://img.shields.io/github/workflow/status/mini-services/miniurl/MiniUrl" alt="Build">
    <img src="https://img.shields.io/github/workflow/status/mini-services/miniurl/Test?label=test" alt="Test">
    <a href="https://codecov.io/gh/mini-services/miniurl"><img src="https://codecov.io/gh/mini-services/miniurl/branch/main/graph/badge.svg?token=YE7LE819HK" alt="Codecov"></a>
    <a href="https://join.slack.com/t/mini-services/shared_invite/zt-lu0mqw8n-HVk1Aq6yUODvJXZ6ikIA9w"><img src="https://img.shields.io/badge/Chat-Slack-blue" alt="Slack Chat"></a>
<a href="https://app.fossa.com/projects/git%2Bgithub.com%2Fmini-services%2Fminiurl?ref=badge_shield" alt="FOSSA Status"><img src="https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmini-services%2Fminiurl.svg?type=shield"/></a>
    <img src="https://img.shields.io/github/license/mini-services/miniurl" alt="License">
     <a href="https://github.com/mini-services/miniurl/issues"><img src="https://img.shields.io/github/issues/mini-services/miniurl" alt="Issues"></a>
     <img src="https://img.shields.io/snyk/vulnerabilities/github/mini-services/miniurl" alt="Analysis">
     <img src="https://img.shields.io/github/last-commit/mini-services/miniurl" alt="Last Commit">
    <a href="https://hub.docker.com/r/miniservices/miniurl">
    <img src="https://img.shields.io/docker/pulls/miniservices/miniurl" alt="Docker Pulls"></a>   
     <img src="https://img.shields.io/docker/image-size/miniservices/miniurl" alt="Docker Image Size"></a>
     <img src="https://img.shields.io/docker/v/miniservices/miniurl" alt="Docker Version"></a>
</p>

# MiniUrl

MiniUrl is an open-source production-ready microservice for shortening Urls. Deploy it on your personal cloud and get a fully-functional url-shortener with zero effort.

MiniUrl is part of the [Mini Services Project](https://github.com/mini-services).

**Features**

-   Extremely efficient
-   Production ready
-   Zero code
-   Easy setup
-   Highly configurable
-   Cloud-ready with an almost zero-config Helm chart
-   Multiple deployment options

## Getting Started

-   Run using [Helm](https://github.com/mini-services/miniurl/tree/main#helm), [Docker](https://github.com/mini-services/miniurl/tree/main#docker) or [Node.js](https://github.com/mini-services/miniurl/tree/main#nodejs)
-   Use the API (future: dashboard) and enjoy a zero-code microservice :upside_down_face:

## Deployment Options

### Helm

MiniUrl maintains an extensive production-grade Helm chart. See the [chart](https://github.com/mini-services/miniurl/tree/main/helm-chart) for the possible values configuration and examples.

```s
helm repo add miniservices https://raw.githubusercontent.com/mini-services/helm-charts/main
helm repo update

# You may also add --set ingress.enable=true for deploying an Ingress route as well
helm upgrade --install miniurl miniservices/miniurl --set baseRedirectUrl=<YOUR_SHORT_URL>
```

### Docker

Run MiniUrl's docker image directly.

```s
docker run -d --name miniurl -e BASE_REDIRECT_URL=<YOUR_SHORT_URL> -e STORAGE_DRIVER=InMemory -p 80:8000 miniservices/miniurl
```

**NOTE** this deployment is NOT production ready since it uses the InMemory storage driver which is a plain object. To run a production-grade docker deployment, you will need to provide a suitable database. A working example using Postgres:

```s
docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres
docker run -d --name miniurl -p 80:8000 miniservices/miniurl \
                             -e BASE_REDIRECT_URL=<YOUR_SHORT_URL> \
                             -e STORAGE_DRIVER=Postgres \
                             -e POSTGRES_STORAGE_DATABASE=postgres \
                             -e POSTGRES_STORAGE_HOST=localhost \
                             -e POSTGRES_STORAGE_USER=postgres \
                             -e POSTGRES_STORAGE_PASSWORD=postgres
```

### Node.js

Requirements:

-   Node.js 14.8+
-   NPM 6+ or Yarn X+

```s
git clone https://github.com/mini-services/miniurl.git
cd miniurl
npm install
npx cross-env BASE_REDIRECT_URL=<YOUR_SHORT_URL> STORAGE_DRIVER=InMemory npm start
```

Node.js troubleshooting:

for Windows OS, it is also possible to use [nvm for Windows](https://github.com/coreybutler/nvm-windows/releases). Refer to the [nvm documentation](https://github.com/coreybutler/nvm-windows/blob/master/README.md).

**NOTE** this deployment is NOT production ready since it uses the InMemory storage driver which is a plain object. To run a production-grade docker deployment, you will need to provide a suitable databases (and possibly a process manager such as [pm2](https://github.com/Unitech/pm2)). A working example assuming a Postgres database on `localhost:5432` with username `postgres` and password `postgres`:

```s

npx cross-env BASE_REDIRECT_URL=<YOUR_SHORT_URL> \
              STORAGE_DRIVER=Postgres \
              POSTGRES_STORAGE_HOST=localhost \
              POSTGRES_STORAGE_USER=postgres \
              POSTGRES_STORAGE_PASSWORD=postgres \
              npm start
```

### Development

See [running the project in development mode](docs/contribution.md#running-the-project-in-development-mode).

## API

The easiest way to get familiar with MiniUrl's API is using [Insomnia](https://insomnia.rest/) or [Postman](https://www.postman.com/).

**Insomnia**

-   [MiniUrl workspace](https://raw.githubusercontent.com/mini-services/miniurl/main/docs/assets/insomnia.json)

**Postman**

-   [Url collection](https://raw.githubusercontent.com/mini-services/miniurl/main/docs/assets/postman/url-collection.json)
-   [Demo environment](https://raw.githubusercontent.com/mini-services/miniurl/main/docs/assets/postman/demo-environment.json)
-   [Local environment](https://raw.githubusercontent.com/mini-services/miniurl/main/docs/assets/postman/local-environment.json)

### POST /miniurl/url

Save a url and get a shorter one instead.

#### Request

Body (json)

-   `url: string` (required) - The url to shorten

#### Response

Body (text) - a shortened Url

### GET /miniurl/url/:id

Retrieves a saved url with its information.

#### Request

Query

-   `id: string` (required) - The saved url's id

#### Response

Body (json)

-   `id: string` - The url's id
-   `url: string` - The saved url
-   `createdAt: string` - an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date indicating the creation time
-   `updatedAt: string` - an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date indicating the last updated time

### GET **<BASE_REDIRECT_URL>**/:id

Redirects to a saved url.

**NOTE** this endpoint may not be found at the root url since it depends on the baseRedirectUrl configuration variable. e.g if the baseRedirectUrl is `localhost:3000/u`, this endpoint will be found at `/u/:id`.

#### Request

Query

-   `id: string` (required) - The saved url's id

#### Response

Redirect 302 - redirects to the saved url.

## Configuration

Since MiniUrl follows the best practices including the [12 factor app](https://12factor.net/), the microservice is entirely configurable via environment variables. Available variables are:

### General

**BASE_REDIRECT_URL** (required) - the shortened urls' base path e.g https://youtu.be, https://bit.ly or https://example.com/u

**API_PREFIX** (default: "/miniurl") - MiniUrl's prefix for the api routes (e.g for saving a url, one would POST to ${API_PREFIX}/url)

**URL_MATCH_PATTERN** (default: "\*\*") - a [micromatch](https://github.com/micromatch/micromatch)-complaint glob pattern for restricting the saved urls (for example, if you don't want your MiniUrl to save links other than your domain such as https://evil-fisching.com)

**URL_LIFETIME** (default: "7 days") - a human-readible time (see the [ms docs](https://github.com/vercel/ms) for available options) stating the url lifetime (after which it expires). Note that the expiration mechanism runs at most once per minute and at least once per hour and so slight deviation may occur.

**PORT** (default: "80") - the Node.js process port. In most cases your shouldn't change this

### Storage

**STORAGE_DRIVER** (required) - MiniUrl's storage driver. available options are `InMemory` (for development purposes only) and `Postgres` (for any Knex.js-complaint SQL database).

**POSTGRES_STORAGE_HOST** (required if STORAGE_DRIVER is `Postgres`) - the Postgres database's host (e.g https://my-database.com)

**POSTGRES_STORAGE_USER** (required if STORAGE_DRIVER is `Postgres`) - the Postgres database's username

**POSTGRES_STORAGE_PASSWORD** (required if STORAGE_DRIVER is `Postgres`) - the Postgres database's password

**POSTGRES_STORAGE_DATABASE** (required if STORAGE_DRIVER is `Postgres`) - the Postgres database's name (e.g postgres)

### Auth

**AUTH_DRIVER** (default: `BearerToken`) - MiniUrl's auth driver. Currently, the only available option is `BearerToken`.

**AUTH_BEARER_TOKEN** (default: <RANDOM>) - The bearer token to use when selecting the `BearerToken` auth driver.

## Issues and Questions

### Issues & Features

If you found a bug or have an idea for a feature, feel free to [open an issue](https://github.com/mini-services/miniurl/issues/new/choose).

### Questions

You may [open an issue](https://github.com/mini-services/miniurl/issues/new/choose) for questions, but it's usually faster to send a message on our [Slack](https://join.slack.com/t/mini-services/shared_invite/zt-lu0mqw8n-HVk1Aq6yUODvJXZ6ikIA9w)

## Contribution

Refer to our [contribution guide](docs/contribution.md).

## License

[MIT](https://opensource.org/licenses/MIT)

[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmini-services%2Fminiurl.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmini-services%2Fminiurl?ref=badge_large)

Copyright (c) 2020-present, Snir Shechter
