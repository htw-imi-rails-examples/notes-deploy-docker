# Travis-Heroku-Docker: This is a Demo App for Heroku Deployment of a Rails app via Travis in a docker container.

## Set up a production docker image

To run rails in production mode, a couple of changes need to be applied:

- Database need to be set to docker
- RAILS_ENV needs to be set to production to run rails in production mode
- assets should be precompiled and included in the docker image.
- the image should be build by travis after an successful test run and uploaded to heroku - thus a script is created that builds the image with an appropriate tag. see travis/docker-upload.sh

To test docker image locally:

start the database, build and start the web app:

    travis/docker-upload.sh
    docker-compose up db
    export SECRET_KEY_BASE=$(rake secret)
    docker run -e SECRET_KEY_BASE=$SECRET_KEY_BASE \
               -p 3002:3000 \
               --name web \
               notes/notes:293ef60

execute bash in container:
    docker exec -ti web bash

## Pushing the image to Heroku
Documentation:

* [Container Registry & Runtime (Docker Deploys)](https://devcenter.heroku.com/articles/container-registry-and-runtime)

https://docs.travis-ci.com/user/docker/#Pushing-a-Docker-Image-to-a-Registry

to get the heroku token into a env variable:

    export HEROKU_TOKEN=$(heroku auth:token)
set the env variable on travis (switch "show it in the log" to off!)

add these steps to travis.yml (can also be tested locally)

- docker login -u _ -p "$HEROKU_TOKEN"  registry.heroku.com
- docker build -t registry.heroku.com/notes/web -f Dockerfile.production .
- docker push registry.heroku.com/notes/web
    docker login -u _ -p $HEROKU_TOKEN registry.heroku.com


add HEROKU_TOKEN to .travis.yml
travis encrypt HEROKU_TOKEN=$(heroku auth:token) --add env.global



https://devcenter.heroku.com/articles/local-development-with-docker-compose
