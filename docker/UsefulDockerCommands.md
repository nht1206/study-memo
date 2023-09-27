# Useful Docker Commands
## Display the untagged images (not intermediary images)
```sh
docker images --filter="dangling=true"
```

## Remove all the untagged images
NOTE: Docker warns you if any container exists using these images
```sh
docker rmi $(docker images --filter="dangling=true" -q)
```

<!-- ```sh
``` -->