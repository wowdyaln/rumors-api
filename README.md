# rumors-api

[![Build Status](https://travis-ci.org/cofacts/rumors-api.svg?branch=master)](https://travis-ci.org/cofacts/rumors-api) [![Coverage Status](https://coveralls.io/repos/github/cofacts/rumors-api/badge.svg?branch=master)](https://coveralls.io/github/cofacts/rumors-api?branch=master)

GraphQL API server for clients like rumors-site and rumors-line-bot


## Development

### Prerequisite

* Docker & [docker-compose](https://docs.docker.com/compose/install/)

### First-time setup

After cloning this repository & cd into project directory, then install the dependencies.

```
$ git clone --recursive git@github.com:MrOrz/rumors-api.git # --recursive for the submodules
$ cd rumors-api
$ npm i
```

If you want to test OAuth2 authentication, you will need to create `config/local-development.js` (already in `.gitignore`) with the following content:

```
module.exports = {
  FACEBOOK_APP_ID: '⋯⋯',
  FACEBOOK_SECRET: '⋯⋯',
  TWITTER_CONSUMER_KEY: '⋯⋯',
  TWITTER_CONSUMER_SECRET: '⋯⋯',
  GITHUB_CLIENT_ID: '⋯⋯',
  GITHUB_SECRET: '⋯⋯',
};
```

Please apply for the keys in Facebook, Twitter and Github respectively.

### Start development servers

```
$ mkdir esdata # For elasticsearch DB
$ docker-compose up
```

This will:

* rumors-api server on `http://localhost:5000`. It will be re-started when you update anyfile.
* Kibana on `http://localhost:6222`.
* ElasticSearch DB on `http://localhost:62222`.

To stop the servers, just `ctrl-c` and all docker containers will be stopped.

### Populate ElasticSearch with data

Ask a team member to send you `nodes` directory, then put the `nodes` directory right inside the
`esdata` directory created in the previous step, then restart the database using:

```
$ docker-compose restart db
```

### Detached mode & Logs

If you do not want a console occupied by docker-compose, you may use detached mode:

```
$ docker-compose up -d
```

Access the logs using:

```
$ docker-compose logs api     # `api' can also be `db', `kibana'.
$ docker-compose logs -f api  # Tail mode
```

### About `test/rumors-db`

This directory is managed by git submodule. Use the following command to update:

```
$ npm run rumors-db:pull
```

## Lint

```
# Please check lint before you pull request
$ npm run lint
# Automatically fixes format error
$ npm run lint:fix
```

## Test

To prepare test DB, first start an elastic search server on port 62223:

```
$ docker run -d -p "62223:9200" --name "rumors-test-db" docker.elastic.co/elasticsearch/elasticsearch:6.1.0
# If it says 'The name "rumors-test-db" is already in use',
# Just run:
$ docker start rumors-test-db
```

Then run this to start testing:

```
$ npm t
```

If you get "Elasticsearch ERROR : DELETE http://localhost:62223/replies => socket hang up", please check if test database is running. It takes some time for elasticsearch to boot.

If you want to run test on a specific file (ex: `src/xxx/__tests__/ooo.js`), run:

```
$ npm t -- src/xxx/__tests__/ooo.js
```


When you want to update jest snapshot, run:

```
$ npm t -- -u
```

## Deploy

Build docker image. The following are basically the same, but with different docker tags.

```
# Production build
$ npm run build

# Staging build
$ npm run build:staging
```

Run the docker image on local machine, then visit `http://localhost:5000`.
(To test functions involving DB, ElasticSearch DB must work as `config/default.js` specified.)

```
$ docker run --rm -it -p 5000:5000 mrorz/rumors-api
```

Push to dockerhub
```
# Production
$ docker push mrorz/rumors-api:latest

# Staging
$ docker push mrorz/rumors-api:staging
```
