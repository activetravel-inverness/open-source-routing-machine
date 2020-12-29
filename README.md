# ActiveTravel Inverness - Open Source Routing Machine
 
Docker configuration for an active-travel router for the Inverness area.

## [osrm-backend-foot](./osrm-backend-foot/Dockerfile)

An extension of the osrm-backend image that provides walking directions
for the Inverness area.

## [osrm-backend-bicycle](./osrm-backend-bicycle/Dockerfile)

An extension of the osrm-backend image that provides cycling directions
for the Inverness area.

## Running the stack

```sh
docker-compose build
docker stack deploy -c docker-compose.yml ati
```
