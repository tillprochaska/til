# Docker

## `ENV` vs `ARG`

* Environment variables set using `ARG` are only available during build time.
* Environment variables set using `ENV` are available while the Docker image is built as well during container runtime.

## Set Kernel settings in Docker VM

Some software may require to change Kernel settings on the host machine. For example, in order to run ElasticSearch, virtual memory limits most likely [need to be increased](https://www.elastic.co/guide/en/elasticsearch/reference/5.0/vm-max-map-count.html#vm-max-map-count). As Docker for Mac uses a virtual machine that acts as the host for containers, those settings need to be set in the VM. Use the following command to access a shell in the VM:

```
nc -U ~/Library/Containers/com.docker.docker/Data/debug-shell.sock
```
