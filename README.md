# Dropbox as docker-compose

Run the Linux Dropbox client inside Docker with persistent storage and configurable user mapping.

This project uses Docker Compose to run the [dropbox-container](https://github.com/g3nsvrv/dropbox-container) image, mapping it to a custom user with configurable UID/GID values, and storing Dropbox data inside a persistent volume.

## Features

* Runs the official Linux Dropbox client inside Docker
* No build step — pulls a pre-built image
* Custom user and group creation, configurable per container
* UID/GID mapping for host filesystem compatibility
* Persistent user home directory using Docker volumes
* No credentials generated or stored

## Project structure

```text
.
├── .env
└── docker-compose.yaml
```

## Configuration

Configuration is stored in `.env`:

```env
USERACCOUNT=dropbox
GROUPACCOUNT=dropbox
HOSTNAME=dropbox-docker
PUID=1000
PGID=100
```

### Variables

| Variable       | Description                              |
| -------------- | ----------------------------------------- |
| `USERACCOUNT`  | Username created inside the container     |
| `GROUPACCOUNT` | Group name created inside the container   |
| `HOSTNAME`     | Container hostname                        |
| `PUID`         | User ID used inside the container         |
| `PGID`         | Group ID used inside the container        |

## Usage

Pull and start:

```bash
docker compose up -d
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

* No password is generated for the created account, and no credentials are stored in the image or container.
* The Dropbox authorization link (needed to link your account on first startup) is shown in the container's stdout (log), viewable with `docker logs <container-name>`.
* Dropbox data persists between container recreations through the Docker volume.
* This project depends on the published [`ghcr.io/g3nsvrv/dropbox-container`](https://github.com/g3nsvrv/dropbox-container) image. To customize or extend the image itself, see that repository instead.

## TODO

* Mount paths and custom volumes

## License

This project is licensed under the Creative Commons Attribution–NonCommercial–ShareAlike 4.0 International (CC BY-NC-SA 4.0).

https://creativecommons.org/licenses/by-nc-sa/4.0/
