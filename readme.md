# Self Hosted Postgres Database

# pgAdmin 4 Directory Permission Issues

## Overview

When running **pgAdmin 4** inside a Docker container with host-bound volume mounts, the container process often fails to start with the following error:

```text
ERROR  : Failed to create the directory /var/lib/pgadmin/sessions:
         [Errno 13] Permission denied: '/var/lib/pgadmin/sessions'

```

This error occurs because the offical pgAdmin 4 image operates as a non-root user with UID `5050` (and GID `5050`). If the directory created on the host machine is owned by `root` or your local user, the container lacks write access to manage sessions, configuration, and storage.

---

## Required Action Steps

### Step 1: Fix Directory Ownership on the Host

Grant ownership of the host volume directory to UID `5050` and GID `5050`.

Run the following command on the host system:

```bash
sudo chown -R 5050:5050 /tmp/sandbox/volumes/embanet/postgres/admin

```

### Step 2: Ensure Proper Directory Permissions

Ensure the directory has full read, write, and execute permissions for the owner:

```bash
sudo chmod -R 700 /tmp/sandbox/volumes/embanet/postgres/admin

```

### Step 3: Restart the Services

Recreate and launch the container stack using Docker Compose:

```bash
docker compose down
docker compose up -d

```

---

## Technical Details

| Parameter | Value |
| --- | --- |
| Container Image | `dpage/pgadmin4` |
| Internal User UID | `5050` (`pgadmin`) |
| Internal User GID | `5050` (`pgadmin`) |
| Mount Location | `/tmp/sandbox/volumes/embanet/postgres/admin` |
| Container Path | `/var/lib/pgadmin` |
