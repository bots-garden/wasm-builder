# Docker Compose With Coder: How to

## Config

The default configuration is located in the `.env` file.
I'm working on a `arm64` architecture. So, ff you need to adapt the values of the environment variables, create an new env file (ex: `arm.env`) and use the `--env-file` flag of Docker Compose.

- Add `.config` to `.gitignore`


## Build
```bash
docker compose build
```

## Run
```bash
# If you use .env
docker compose up -d
# If you use arm.env
docker compose --env-file ./arm.env up  -d
```
Then: 
- Open a terminal, and run `./connect.sh`
- Type this command `git config --global --add safe.directory /garden.bots.dev`
- And configure **git**:
  ```bash
  git config --global user.name @your-handle
  git config --global user.email your@e.mail
  ```

### Connect to the container
```bash
#set -o allexport; source .env; set +o allexport
set -o allexport; source arm.env; set +o allexport
docker exec --workdir /${WORKDIR} -it ${CONTAINER_NAME} \
/bin/bash
```

## Stop
```bash
docker compose down
```
