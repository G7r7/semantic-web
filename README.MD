
# Fuseki server
```shell
docker compose run --rm --name MyServer --service-ports fuseki --file /turtles/tpX/exerciseX/exerciseX.ttl /ds
```

# Jupyter notebook
```shell
docker run -p 8888:8888 -v ./work: rpersee/linked-data-notebook:latest
```