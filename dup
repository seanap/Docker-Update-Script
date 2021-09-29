#!/bin/bash
cd

docker-compose down

docker-compose pull

docker-compose up -d --force-recreate

docker rmi $(docker images -f "dangling=true" -q) -f
