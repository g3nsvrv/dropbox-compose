# docker-compose

Run the Linux Dropbox client inside Docker with persistent storage and configurable user mapping.

This project builds a Docker image that installs the Dropbox daemon on top of a configurable Debian-based image, creates a user with custom UID/GID values, and stores Dropbox data inside a persistent volume.

## Features

* Runs the official Linux Dropbox client inside Docker
* Configurable base image
* Custom user and group creation
* UID/GID mapping for host filesystem compatibility
* Persistent user home directory using Docker volumes
* Automatically downloads and installs Dropbox

## Project structure

```text
.
├── .env
├── docker-compose.yaml
└── dropbox/
    └── Dockerfile
```

## Configuration

Configuration is stored in `.env`:

```env
CONTEXT=dropbox
IMAGE=debian:stable-slim
USERACCOUNT=dropbox
HOSTNAME=dropbox-docker
UID=1000
GID=100
```

### Variables

| Variable      | Description                           |
| ------------- | ------------------------------------- |
| `CONTEXT`     | Docker build context                  |
| `IMAGE`       | Base image used for the build         |
| `USERACCOUNT` | Username created inside the container |
| `HOSTNAME`    | Container hostname                    |
| `UID`         | User ID used inside the container     |
| `GID`         | Group ID used inside the container    |

## Usage

Build and start:

```bash
docker compose up -d --build
```

Check logs:

```bash
docker logs <container-name>
```

On first startup Dropbox will display an authorization URL. Open it in a browser and link your Dropbox account.

## Storage

The compose file creates a persistent volume:

```yaml
volumes:
  dropbox-home:
```

This volume is mounted as:

```text
/home/${USERACCOUNT}
```

The entire Dropbox user environment is persisted, including:

* Dropbox files
* Dropbox account configuration
* Dropbox application data

## Notes

* The image generates a random password for the created user during build time.
* The generated password is written to:

```text
/var/log/user-<username>.password
```

* The password is also printed to the container's stdout (log), viewable with `docker logs <container-name>`.
* The Dropbox authorization link (needed to link your account on first startup) is also shown in the container's stdout (log), viewable with `docker logs <container-name>`.

* Dropbox data persists between container recreations through the Docker volume.

## TODO

* Mount paths and custom volumes

## License

This project is licensed under the Creative Commons Attribution–NonCommercial–ShareAlike 4.0 International (CC BY-NC-SA 4.0).

https://creativecommons.org/licenses/by-nc-sa/4.0/