<p align="center">
  <img src="./logo.png" height="150">
</p>

<h1 align="center">Docker</h1>

<p align="center">
  Docker is a platform and technology that enables developers to create, deploy, and manage applications using containers.
</p>

<p align="right">
  <a href="../#tech-stack">Main Page ↖</a>
</p>

### Contents

1. [Installation](#-installation)
2. [Docker basics](#-docker-basics)
   - [Images](#-images)
   - [Containers](#-containers)
   - [Dockerfile](#-dockerfile)
     - [Basic instructions](#-basic-instructions)
     - [Arguments instructions (Build time)](#-arguments-instructions-build-time)
     - [Environment Variables (Runtime)](#-environment-variables-runtime)
3. [Data & Volumes](#-data--volumes)
   - [Bind mounts (Host & Container)](#-bind-mounts-host--container)
   - [Named volumes (Container & Container)](#-named-volumes-container--container)
   - [Anonymous volumes](#-anonymous-volumes)
4. [Docker Compose](#-docker-compose)
   - [Installation (docker compose)](#-installation-docker-compose)
   - [The Compose file](#-the-compose-file)
   - [Commands](#-commands)
5. [Docker Networking](#-docker-networking)

<br>

---

## 🔶 Installation

1. Setting up Linux repositories for the latest Docker version & Installation: [Docs ↗](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

2. To run Docker without root privileges: [Docs ↗](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

3. (optional) Configure Docker to start on boot: [Docs ↗](https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot-with-systemd)

<p align="right">
    <a href="#docker">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Docker basics

> Docker CLI official docs: https://docs.docker.com/engine/reference/commandline/cli/

- `docker command --help`: Shows detailed information and usage instructions about a specific Docker command.
- `docker system prune`: Frees up storage by clearing stopped containers, unused networks, dangling images, and build cache.
- `docker inspect <Object>`: Provides detailed, low-level information about Docker objects such as containers, images, volumes, and networks.

<br>

### 🔷 Images

A Docker image is a blueprint or template that includes both the application and the necessary environment to run it. This environment encompasses the operating system, runtimes, tools, and more.

- `docker pull <Image_Name>`: Downloads an image from DockerHub or another registry.
- `docker images`: Displays a list of all images stored locally.
- `docker image tag <Name>:<Tag> <New_Name>:<New_Tag>`: Renames an image.
- `docker tag <Name>:<Tag> <New_Name>:<New_Tag>`: Creates a clone of an image with a new name and optional tag.
- `docker rmi <Image_Name>`: Deletes an image by its name.
  > Any connected containers must be removed first.
- `docker image prune <-Options>`: Deleting images in bulk.
  - ` ` - Removes all dangling images (untagged images).
  - `-a` - Removes all locally stored images.
- `docker build <-Options>...`: Building new custom images.
  - `.` - Builds an image using the Dockerfile in the current directory.
    > path_to_Dockerfile
  - `-f path/to/customFile` - Uses a specific Dockerfile for building (default: Dockerfile).
  - `-t name:tag` - Assigns a NAME and TAG to the image (e.g., node:16.5, node:latest).

<br>

### 🔷 Containers

Containers are running instances of images. Multiple containers can be started based on the same single image. All containers run in isolation, meaning they don't share any application state or written data by default.

> - **Attached mode**: The listening or foreground mode.
> - **Detached mode**: The background mode.
> - **Interactive mode**: The input mode.

- `docker run <-Options>... <Image_Name>`: Creates and starts a new container (in attached mode) based on the specified image.
  > Use `docker create <Image_Name>` if you want to create a container without starting it immediately. The image can also be a remote image, which Docker will automatically download.
  - `--name <Container_Name>` - Assigns a name to the container, making management easier.
    > Otherwise, you would need to use the container ID for reference.
  - `-w <Dir_Address>`: Specifies the working directory inside the container.
    > Any default commands executed in the container will be relative to this directory.
  - `-d` - Runs the container in detached mode, freeing your terminal.
  - `-it` - Runs the container in interactive mode (combining interactive and user-friendly terminal modes).
  - `--rm` - Automatically removes the container when it's stopped.
  - `-p <External_Port>:<Internal_Port>` - Exposes a Docker port to the outside world.
    > Incoming requests to `External_Port` on the local host are routed to `Internal_Port` inside the container, where the application should be listening.
- `docker container ls <-Options>`: Displays a list of all running containers along with their specifications.
  - `-a` - Displays a list of all containers, including those that are stopped.
- `docker stop <Container_Name>`: Gracefully stops a running container (SIGTERM).
- `docker kill <Container_Name>`: Forcefully stops a running container instantly (SIGKILL).
- `docker start <Container_Name> <-Options>`: Starts a stopped container (detached mode).
  > Unlike `docker run`, this command starts the existing container.
  - `-a` - Starts the container in attached mode.
  - `-i` - Starts the container in interactive mode.
- `docker attach <Container_Name>`: Attaches to a running container.
  > This is useful to reattach if previously detached.
- `docker stats <Container_Name>`: Displays detailed information about CPU, memory, and other resource usage for a specific container.
- `docker logs <Container_Name> <-Options>`: Shows previous logs for a container.
  > You can also use this on a stopped container.
  - `-f` - Follows logs in real-time.
    > This is useful for monitoring a running container (similar to attached mode).
- `docker rm <Container_Name>`: Deletes a container using its name.
- `docker container prune`: Deletes all stopped containers.

<br>

**Executing Commands within a Container:**

- `docker run <Image_Name> <Command>`: Overrides the default command and run a new container.

  > The default command in Docker refers to the command specified in the Dockerfile's `CMD` instruction. This command is executed when a container is started from an image using the `docker run` command without explicitly providing a command.

  > If a default command is set and the container is stopped and then started again, the originally defined default command (from the Dockerfile) will be executed. This command cannot be altered; the only option is to create a new container.

- `docker exec <-Options>... <Container_Name> <Command>`: Executes an additional command within a running container.

Examples:

> - `docker run myImage ls`: Creates a new container and runs the command to list all folders within the current directory of the container, overriding the default command.
> - `docker exec -it myContainer redis-cli`: Opens a redis-cli console within a running container.
> - `docker exec -it myContainer sh`: Connects to the shell (terminal) of a running container.
> - `docker run -it myImage sh`: Creates a new container, overriding the default command, and connects to its shell (terminal).

> [!NOTE]
> The commands should be available in the image. For example, you cannot use `redis-cli` if Redis is not available in that image.

<br>

### 🔷 Dockerfile

A Dockerfile is a text-based script used to define the configuration and steps required to build a Docker container image.

> A Dockerfile typically consists of a series of instructions that guide the process of creating a container image. These instructions include things like specifying a base image, adding files and directories, installing software packages, configuring environment variables, and defining how the container should run when launched.

> Dockerfile official docs: https://docs.docker.com/engine/reference/builder/

<br>

#### 🔻 Basic instructions

- `FROM <Image_Name>:<Tag>`: Specifies the base image for your Docker image.
  > It forms the foundation of your container. If no tag is specified, it defaults to "latest".
- `WORKDIR <Dir_Address>`: Sets the working directory for the subsequent instructions in the Dockerfile.
  > This becomes the location where your project will reside within the container. Any following commands executed in the container or instructions will be relative to this directory.
- `COPY <From_Dir> <To_Dir>`: Copies files or directories from the local host to the specified location within the container.
  > For example, using `COPY ./ ./`: it takes files from the current directory on the host and copies them to the same relative location within the container's WORKDIR. This allows you to transfer your project files into the container for further processing.

> [!NOTE]
> During image building, Docker captures a snapshot of the source code at that specific moment. If you modify the source code after creating the image, those changes will not automatically reflect within the existing image. To incorporate the changes, you must rebuild the Docker image to create a new snapshot with the updated source code.

- `RUN <Commands>`: Executes specified commands within the container during the image build process.
  > This instruction allows you to install software, configure settings, and perform various tasks required for setting up the environment within the container. You can include multiple RUN instructions in your Dockerfile to perform a sequence of tasks.
- `EXPOSE <Port>`: Informs Docker that the container will listen on the specified port at runtime.
  > This does not actually publish the port externally; it's used to indicate which ports the container will use. It helps in documenting which ports are intended to be used by the containerized application, making it easier to understand the container's networking requirements. Actual port mapping to the host system is typically done when running the container.
- `CMD ["<Command1_a>", "<Command1_b>", "<Command1_c>"]`: Specifies the default command to run when the container starts.
  > It can include multiple commands as a list. If multiple CMD instructions are provided, the last one will overwrite any previous ones. This sets the initial command or process that the container will execute when it's launched without any specific command provided.
- `ENTRYPOINT ["<Entry_Command>"]`: Defines the primary command that will be executed when the container starts.
  > Unlike CMD, which can be overridden by providing a command when running the container, the ENTRYPOINT instruction sets a fixed command that cannot be easily overridden. Any additional arguments provided when running the container will be passed as arguments to the ENTRYPOINT command. This is useful when you want to create a container that always runs a specific command or application and only accepts additional arguments to modify its behavior.

<br>

#### 🔻 Arguments instructions (Build time)

Dockerfile Arguments (also known as build-time variables or ARGs) are a way to parameterize the Docker image build process.

> They allow you to pass values into the Docker build process, which can then be used within the Dockerfile to customize the image being built.

> Once you define an argument using the ARG instruction, you can reference its value in subsequent instructions.

> You can also override Dockerfile ARGs when building an image using the docker build command. When you pass an argument to the docker build command using the `--build-arg` flag, it will override the default value specified in the Dockerfile.

- Dockerfile: `ARG <Arg_Name>=<Value>`
  ```Dockerfile
  ARG PACKAGE=nginx
  ```
- CLI: `--build-arg <Arg_Name>=<Value>`
  ```
  docker build --build-arg PACKAGE=apache2 ...
  ```

Example usage:

```Dockerfile
RUN apt-get update && apt-get install -y ${PACKAGE}
```

<br>

#### 🔻 Environment Variables (Runtime)

Environment variables are dynamic values that can affect the behavior of processes or applications running on a computer system.

> Environment variables provide a way to customize the behavior of software without modifying the program's source code. They are particularly useful for configuration purposes and for keeping sensitive information, such as API keys or database credentials, separate from the actual codebase.

> For instance, you can access environment variables in a Node.js application using `process.env.var_name`.

There are methods for passing dynamic values into the container during the build process or while the container is running:

- Dockerfile: `ENV <Var_Name> <Value>`

  ```Dockerfile
  ...
  ENV PORT 80

  # You can also use declared ENV variables inside the Dockerfile.
  EXPOSE $PORT
  ...
  ```

- CLI: `--env <Var_Name>=<Value>`
  ```bash
  docker run ... --env PORT=8000 ...
  ```
- .env file (Recommended):
  ```bash
  # example.env
  PORT = 8000
  ```
  And then in CLI:
  ```bash
  docker run ... --env-file ./env/file/path/example.env
  ```

<br>

> [!IMPORTANT]
> There is a mechanism called Dockerfile caching that optimizes the build process of Docker images.
>
> Each instruction in the Dockerfile corresponds to a step in the image creation process. Every time an instruction is executed in the Dockerfile, it generates content that contributes to a new layer, which is then cached. These layers are stacked on top of each other to form the final image. Each layer is immutable, and subsequent layers can only add or modify files on top of the lower layers.
>
> When building an image, Docker attempts to reuse previously built layers if the build context (the directory containing the Dockerfile and other necessary files) hasn't changed. This reuse prevents redundant execution of unchanged steps, thereby speeding up the build process.
>
> Note: modifying a single instruction or related file context **invalidates** subsequent layers. Therefore, optimizing the instruction order can enhance caching benefits.
>
> Watch this: https://youtu.be/RP-z4dqRTZA

<p align="right">
    <a href="#docker">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Data & Volumes

Containers are generally ephemeral and designed to be stateless, which means that the data within a container may not be preserved when the container stops or is removed.

Volumes and data management mechanisms are used to overcome this limitation and to ensure data persistence.

- `docker volume ls`: Lists volumes.
- `docker volume inspect <Volume_Name>`: Displays detailed information about a specific volume.
- `docker volume rm <Volume_Name>`: Deletes the specified volume.
- `docker volume prune`: Deletes all dangling anonymous volumes.

<br>

#### 🔻 Bind mounts (Host & Container)

A bind mount is a way to mount a file or directory from the host machine into a container. This allows the container to access and manipulate files on the host system, and vice versa.

> Bind mounts are often used for development purposes.

- `docker run ... -v </host/path>:</container/path>`: Sets up a bind mount.

  > With this setup, any changes made to the files or directories in the bind mount location on either the host or the container will be reflected on both sides.

  > If you don't want to consistently copy and use the full path, you can use: `-v $(pwd):</container/path>` (Linux only).

<br>

**Read only:**

By default, binding containers have `read`/`write` access to the local machine, which is not recommended as it can lead to unintended changes to local source files. To prevent the container from modifying local source files, you can add `:ro` to the binding configuration.

- `docker run ... -v </host/path>:</container/path>:ro ...`: Establishes a bind mount while enforcing read-only access.

<br>

#### 🔻 Named volumes (Container & Container)

Named volumes are a way to manage and persist data outside of containers while making it easily accessible to multiple containers.

They are used to store data that needs to be shared and persisted across container instances, even when those containers are removed or recreated.

- `docker run ... -v <Volume_Name>:</container/path> ...`: Sets up a named volume.

  > If you create another container using the same named volume (paths can be different) `-v <Volume_Name>:</another/container/path>`, both containers will have access to the same data in the specified folders.

<br>

> [!NOTE]
> You're not limited to a single data storage solution. You can also use multiple of them together or combine them.
>
> Example: `docker run ... -v /srv/app:/app:ro -v test_vol:/var/test ...`

> [!IMPORTANT]
> There is a concept called "more specific path wins" that comes into play when dealing with overlapping or conflicting Docker volume mounts, especially when we use multiple of them together or combine them.
>
> Consider a scenario where you need to bind a folder but exclude specific subfolders. By setting up these particular subfolders as named or anonymous volumes, you ensure their availability in the container's external bound directory without requiring them to be present in your host machine's bound folder.
>
> For Example: `docker run ... -v /var/server:/srv/www ...` Locally (on the host), we don’t have a 'node_modules' folder. This means that when we perform a bind mount, the container will attempt to synchronize itself with the host. However, we don’t have a 'node_modules' directory on our host side, which leads to a crash in the application. We have an exception: the 'node_modules' directory doesn't need to be synchronized. This issue can be resolved by using a named or anonymous volume with a more specific path: `docker run ... -v /var/server:/srv/www -v /srv/www/node_modules ...`

<br>

#### 🔻 Anonymous volumes

Anonymous volumes are a type of Docker volume that is created and managed by Docker itself, rather than being explicitly named or specified by the user.

- `docker run ... -v </container/path> ...`: Creates an anonymous volume.
  > Or, in Dockerfile:
  >
  > ```Dockerfile
  >  VOLUME ["</container/path>"]
  > ```

> A practical use case for anonymous volumes these days is when they are combined with Bind Mounts to exclude specific subfolders. Anonymous volumes are deleted automatically when the container is deleted.

<p align="right">
    <a href="#docker">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Docker Compose

Docker Compose is a tool that allows you to define and manage multi-container Docker applications using a simple YAML file.

> It provides a way to define the services, networks, and volumes required for your application in a single configuration file, making it easier to set up and manage complex applications that consist of multiple interconnected containers.

<br>

#### 🔻 Installation (docker compose)

You don't need to do anything if you install Docker as shown in the [Docker Installation](#-installation) section. The `docker-compose-plugin` includes everything you need to work with Docker Compose.

> Otherwise, check the official installation guide: https://docs.docker.com/compose/install/

<br>

#### 🔻 The Compose file

> Official docs: https://docs.docker.com/compose/compose-file/

**Key components of a Docker Compose file:**

- services: [All service options ↗](https://docs.docker.com/compose/compose-file/05-services/)
  > Defines the different services that make up your application. Each service corresponds to a Docker container. Services can be linked together, specify image names, set environment variables, define ports to expose, and more.
- networks: [All network options ↗](https://docs.docker.com/compose/compose-file/06-networks/)
  > Defines custom networks that can be used to connect services. Containers within the same network can communicate with each other directly by using the service names as hostnames.
- volumes: [All volume options ↗](https://docs.docker.com/compose/compose-file/07-volumes/)
  > Specifies the volumes that should be mounted in the containers. Volumes are used for persistent data storage and sharing between containers.

<br>

**Examples:**

<details><summary>Example #1</summary>

```yaml
services:
  my_app: # The name of the service
    build: . # Build the Docker image from the Dockerfile in the current directory
    stdin_open: true # Keep STDIN open even if not attached (useful for development/debugging)
    tty: true # Allocate a pseudo-TTY (terminal) for the container (keeps the container running)
    ports: # Expose ports to the host machine
      - "3000:80" # Map port 3000 on the host to port 80 in the container
      - "9229:9229" # Map port 9229 on the host to port 9229 in the container
    volumes:
      - ./:/srv/www # Mount the current directory to /srv/www in the container (bind mount).
```

</details>

<details><summary>Example #2</summary>

```yaml
services:
  app:
    container_name: my-app # Name of the container
    image: node:22.18.0-alpine # Use the given image as the base for the container
    ports:
      - "3000:3000"
    volumes:
      - ./:/srv/www
    working_dir: "/srv/www/app" # Set working directory inside the container. So that commands run in this context
    command: npm run dev # Define the command to run when the container starts
    stdin_open: true
    tty: true
    depends_on:
      - database # Wait for the database service to start first
  database: # The name of the service #2
    container_name: my-database
    restart: always # Always restart the container if it stops
    image: postgres:17.5-alpine
    ports:
      - "5432:5432"
    environment: # Environment variables for the database
      POSTGRES_USER: myuser
      POSTGRES_DB: mydb
      POSTGRES_PASSWORD: password123321
    volumes:
      - db_data:/var/lib/postgresql/data # Persist database data on the host (named volume)
volumes:
  db_data: # Define a named volume for database persistence (you need to define it here because db_data is a named volume)
```

</details>

<br>

#### 🔻 Commands

> Docker Compose CLI docs: https://docs.docker.com/compose/reference/

- `docker compose <Command> --help`: Displays available options and their descriptions for the specified Docker Compose command.
- `docker compose up <-Options>...`: Creates and starts containers.
  - `-d` - Starts containers in detached mode.
  - `--build` - Forces Docker Compose to re-evaluate and rebuild all images specified in the compose file.
    > This is done even if an image already exists, ensuring that the most up-to-date images are used.
  - `<service1> <service2> <service3>` - Starts only the selected services.
- `docker compose down <-Options>...`: Stops and removes all containers.
  - `-v` - Removes all volumes associated with the containers.
  - `<service1> <service2> <service3>` - Stops and removes only the selected services
- `docker compose ps`: Displays the status of services defined in the compose.yaml file.
- `docker compose ls`: Lists the running Docker Compose containers.
- `docker compose build`: Builds Docker images specified in the compose.yaml file without starting any services.
- `docker compose run <-Options> <Service_Name> <Command>`: Starts the specified service defined in the docker-compose.yml file and runs the provided command within that service's container.
  - `--rm` - Removes the service's container automatically after it's stopped.
- `docker compose cp <Service_Name>:<path/container/folder> <path/host/folder>`: Copies files/folders between a service container and the host.

<p align="right">
    <a href="#docker">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Docker Networking

Docker networking refers to the mechanisms and features within the Docker platform that allow containers to communicate with each other and with external networks.

- **Container ➡ WWW**

  > Docker containers, by default, can access the internet unless network restrictions are specifically configured.

- **Container ➡ Host Machine**

  > Containers can connect to services running on the host machine using the special hostname:
  >
  > ```
  > host.docker.internal
  > ```
  >
  > or the host's IP address in the bridged network configuration.

  > Example: Let's say you have a Node.js application running in a Docker container, and you want to fetch some data from a locally hosted web server on your host machine.
  >
  > ```js
  > const response = await fetch("http://host.docker.internal:3000/data");
  > ```

- **Container ➡ Container**

  Docker provides various networking options to facilitate communication between containers.

  - **Solution #1**: Using IP Addresses (Not Recommended).

    > You can discover a container's IP address by running `docker container inspect <Container_Name>`, and then employ it to establish communication with the target container.
    >
    > Example: From the Node.js app container to the MongoDB container.
    >
    > ```js
    > mongoose.connect(
    >  "mongodb://IP_ADDRESS:27017/myDB",
    >  ...
    > );
    > ```

  - **Solution #2**: Using container names.

    > Containers within the same network can communicate with each other using their designated container names.

    - `docker network create <Network_Name>`: Creates a network with the given name.

    - `docker run ... --network <Network_Name> ...`: Connects a container to a specific network.

    > Example: From the Node.js app container to the MongoDB container. Assuming we've assigned the name "mymongo" to the MongoDB container using the `--name` flag.
    >
    > ```js
    > mongoose.connect(
    >  "mongodb://mymongo:27017/myDB",
    >  ...
    > );
    > ```

  - **Solution #3**: Using service names (Docker Compose).

    > When using Docker Compose, networks are automatically established, allowing all services defined within the compose file to seamlessly communicate using their service names. Furthermore, network settings can be customized through the compose file. Check [The Compose file](#-the-compose-file) section.

    > ```js
    > const client = redis.createClient({
    >   host: "redis-server",
    >   port: 6379,
    > });
    > ```

  > [!NOTE]
  > You don’t need to expose any ports when there is only a container-to-container connection. Internal networking can function without requiring any port exposure.

- **Host Machine ➡ Container**
  > To allow the host machine to access services running inside a container, the container just needs to expose the relevant ports.

<br>

If you're interested in delving deeper into Docker Networking, I recommend starting by watching this video: https://youtu.be/bKFMS5C4CG0.

<p align="right">
    <a href="#docker">back to top ⬆</a>
</p>

<br>
<br>
