# Docker

## `ENV` vs `ARG`

* Environment variables set using `ARG` are only available during build time.
* Environment variables set using `ENV` are available while the Docker image is built as well during container runtime.

## Set Kernel settings in Docker VM

Some software may require to change Kernel settings on the host machine. For example, in order to run ElasticSearch, virtual memory limits most likely [need to be increased](https://www.elastic.co/guide/en/elasticsearch/reference/5.0/vm-max-map-count.html#vm-max-map-count). As Docker for Mac uses a virtual machine that acts as the host for containers, those settings need to be set in the VM. Use the following command to access a shell in the VM:

```
nc -U ~/Library/Containers/com.docker.docker/Data/debug-shell.sock
```

## Mounting a folder *from* a container *to* the host system

In In some cases it might be required to map the contents of a directory in a container to a directory on the host system. For example, you might be working on a TypeScript project and would like to a use a language server to get type information in your editor. The language server needs to be able to find installed packages in order to provide informations (and most likely it will complain if it can’t find imported packages). Thus it’s not enough to simply install packages in the container, they also need to be available from the host file system for the language server to be able to discover them.

The first attempt to achieve this might be using Docker’s bind mounts, for example, defining volumes in a `docker-compose.yml` like this:

```yml
app:
  image: tillprochaska/example
  command: npm start
  volumes:
    - ./node_modules:/app/node_modules
```

This doesn’t work, though. Even if the image does indeed contain all packages in `/app/node_modules`, after running `docker compose up` that directory will be empty. That’s because with bind mounts, the host system’s directory is authorative -- its contents will override the contents of the container’s directory.

A workaround is to copy the packages to a separate location at build time, and then copying them back when starting a container, e.g. like this:

```dockerfile
# Dockerfile
RUN npm install
RUN cp node_modules /node_modules_cache
```

```yml
# docker-compose.yml
app:
  image: tillprochaska/example
  command: bash -c 'cp /node_modules_cache node_modules && npm start'
  volumes:
    - ./node_modules:/app/node_modules
```

This works because the command is executed after the container and all bind mounts all volumes have been mounted. However, it can be slow to copy all dependencies every time the container is started, especially when the `node_modules` directory is quite large (which it likely is, because JavaScript).

Alternatively, `rsync` can be used. It will be way faster if most of the files already exist on the host system (and thus in the container directory as well):

```yml
# docker-compose.yml
app:
  image: tillprochaska/example
  command: 'bash -c 'rsync -a /node_modules_cache node_modules && npm start'
  volumes:
    - ./node_modules:/app/node_modules
```
