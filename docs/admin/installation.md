# Installation
## Docker Compose

???+ tip
    This is the recommended way to run the application.

### Main services
The main components of the YouGrade system are the Hub and the Operator. The Hub is the frontend application that users interact with, while the Operator is the backend that manages the execution of tasks. Both components require a PostgreSQL database to store their data. Here is a sample `docker-compose.yml` file that starts the Hub, the Operator, and a PostgreSQL database:
```yaml
---
services:
  db:
    image: postgres:16
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=$POSTGRES_USER
      - POSTGRES_PASSWORD=$POSTGRES_PASSWORD
      - POSTGRES_DB=$POSTGRES_DB
    command: -c max_connections=2000
    restart: unless-stopped
    # ports:
    #   - "5432:5432" # uncomment if you need outside database access
  hub:
    image: ghcr.io/yougrade/hub:latest
    container_name: yougrade
    environment:
      - DATABASE_URL=postgresql://$POSTGRES_USER:$POSTGRES_PASSWORD@db:5432/$POSTGRES_DB_HUB?schema=public
      - NEXT_PUBLIC_BASE_URL=$HUB_BASE_URL
      - NEXTAUTH_URL=$HUB_BASE_URL
      - NEXT_PUBLIC_INTERNAL_BASE_URL=$HUB_INTERNAL_BASE_URL
      - OIDC_PROVIDER_NAME=$OIDC_PROVIDER_NAME
      - OIDC_ISSUER=$OIDC_ISSUER
      - OIDC_AUTHORIZATION_URL=$OIDC_AUTHORIZATION_URL
      - OIDC_TOKEN_URL=$OIDC_TOKEN_URL
      - OIDC_USER_INFO_URL=$OIDC_USER_INFO_URL
      - OIDC_CLIENT_ID=$OIDC_CLIENT_ID
      - OIDC_CLIENT_SECRET=$OIDC_CLIENT_SECRET
      - OIDC_CALLBACK_URL=$HUB_BASE_URL/api/auth/callback
      - OIDC_SCOPE=$OIDC_SCOPE
      - NEXTAUTH_SECRET=$AUTH_SECRET
      - OPERATOR_URL=http://operator:5000
      - OPERATOR_API_TOKEN=$OPERATOR_API_TOKEN
    restart: unless-stopped
    # ports:
    #   - "3000:3000" # Uncomment if you don't use a proxy network
    volumes:
      - "uploads:/app/uploads"
    depends_on:
      - db
    networks:
      - proxy # Uncomment if you don't use a proxy network
      - default
      - yougrade_operator_runners

  operator:
    image: ghcr.io/yougrade/operator-docker:latest
    container_name: yougrade-operator
    environment:
      - DATABASE_URL=postgresql://$POSTGRES_USER:$POSTGRES_PASSWORD@db:5432/$POSTGRES_DB_OPERATOR?schema=public
      - OPERATOR_URL=http://operator:5000
      - INTERNAL_OPERATOR_URL=$INTERNAL_OPERATOR_URL
      - MEMORY_LIMIT=$MEMORY_LIMIT
      - MAX_CPU_CORES=$MAX_CPU_CORES
      - HUB_TOKEN=$OPERATOR_API_TOKEN
      - RUNNER_NETWORK_NAME=yougrade_operator_runners
    restart: unless-stopped
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    depends_on:
      - db
    networks:
      - default
      - yougrade_operator_runners

volumes:
  postgres-data:
  uploads:

networks:
  yougrade_operator_runners:
    name: "yougrade_operator_runners"
    ipam:
      config:
        - subnet: 172.33.0.0/16 # adjust to your needs
  proxy: # replace with your proxy network
    external: true
```

This will deploy the application.

### Initializing the Database
Currently, it is required to initialize the database manually. Therefore, for both operator and hub, clone the repository, adjust the `DATABASE_URL` variable in the `.env` file, install the dependencies with `yarn install` and run the following commands:
```bash
yarn generate
prisma db push
```
Then, just on the hub, also run `yarn db:seed` to populate the database with some example data.
### Grader Images
For public docker images from docker hub or ghcr, you don't have to do anything. They will be pulled automatically when needed. If you want to use your own images however, you have to ensure that they are available on the host machine. For example, you can build the reference Jagr image by cloning the `feature/lab-runner` branch of the [Jagr repository](https://github.com/sourcegrade/jagr/tree/feature/lab-runner) and building the image with:
```bash
docker buildx build -t jagr-lab-runner:latest .
```
Alternatively, you can build the image on a different system, and then export it with:
```bash
docker image save jagr-lab-runner:latest | pigz > jagr-lab-runner_latest.tar.gz
```
and then import it on the host machine with:
```bash
pigz -d -c jagr-lab-runner_latest.tar.gz | docker image load
```

## Local Development

The local development is tailored towards VS-Code for Hub and Operator, and IntelliJ Idea for the Runner. It can be adapted to other IDEs though. The following steps are required to set up the local development environment:

- clone the [Jagr repository](https://github.com/sourcegrade/jagr/tree/feature/lab-runner)
- build the image with `docker buildx build -t jagr-lab-runner:latest .`
- clone the [hub](https://github.com/yougrade/hub) and [operator](https://github.com/yougrade/operator)
- create a docker network for the runners with 
```bash
docker network create --subnet=172.21.0.0/16 --gateway=172.21.0.1 yougrade_operator_runners
```
- (adjust the subnet to your needs)
- start the database with `docker compose -f docker-compose.dev.yml up -d` in the hub repository
- for each:
    - install the recommended vscode extensions (see the `.vscode/extensions.json` file)
    - install the dependencies with `yarn install`
    - populate the `.env` files
    - populate the database with `yarn generate` and `prisma db push`
    - on the hub only, run `yarn db:seed` to populate the database with some example data
    - run the application with `yarn dev`
