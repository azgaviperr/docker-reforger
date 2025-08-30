# Arma Reforger Dedicated Server

A dedicated server for Arma Reforger that updates to the latest version every time it restarts.

## Usage

### Docker CLI

```sh
docker create \
  --name=reforger-server \
  -p 2001:2001/udp \
  -v path/to/configs:/reforger/Configs \
  -v path/to/profiles:/home/profile \
  -v path/to/workshop:/reforger/workshop \
  -e SERVER_PUBLIC_ADDRESS="public ip" \
  -e GAME_NAME="My Docker Reforger Server" \
  ghcr.io/acemod/arma-reforger:latest
```

> **Note**: If an admin password is not provided, one will be generated and printed to the console.

### Docker Compose

Use the [examples in the `docker-compose-examples` folder](docker-compose-examples) to get started. Adjust the provided examples to suit your needs.

To start the server, navigate to the folder containing your chosen `docker-compose.yml` file and run:

```bash
docker-compose up -d
```

To stop the server, run:

```bash
docker-compose down
```

## Configuration

### Environment Variables

The following environment variables can be used to configure the server:

| Variable                               | Default Value                                 | Description                                                             |
| -------------------------------------- | --------------------------------------------- | ----------------------------------------------------------------------- |
| `STEAM_USER`                           | (empty) - will use anonymous login                                       | Steam username for downloading the server files.                        |
| `STEAM_PASSWORD`                       | (empty)                                       | Steam password for the specified user.                                  |
| `STEAM_APPID`                          | `1874900`                                     | Steam App ID for the server. Use `1890870` for the experimental server. |
| `STEAM_BRANCH`                         | `public`                                      | Steam branch to use (e.g., `public`, `beta`).                           |
| `STEAM_BRANCH_PASSWORD`                | (empty)                                       | Password for accessing a private Steam branch.                          |
| `ARMA_CONFIG`                          | `docker_generated`                            | Path to the server configuration file.                                  |
| `ARMA_PROFILE`                         | `/home/profile`                               | Path to the server profile directory.                                   |
| `ARMA_BINARY`                          | `./ArmaReforgerServer`                        | Path to the server binary.                                              |
| `ARMA_PARAMS`                          | (empty)                                       | Additional parameters to pass to the server binary.                     |
| `ARMA_MAX_FPS`                         | `120`                                         | Maximum server FPS.                                                     |
| `ARMA_WORKSHOP_DIR`                    | `/reforger/workshop`                          | Path to the workshop directory.                                         |
| `SERVER_BIND_ADDRESS`                  | `0.0.0.0`                                     | Address to bind the server to.                                          |
| `SERVER_BIND_PORT`                     | `2001`                                        | Port to bind the server to.                                             |
| `SERVER_PUBLIC_ADDRESS`                | (empty)                                       | Public IP address of the server.                                        |
| `SERVER_PUBLIC_PORT`                   | `2001`                                        | Public port of the server.                                              |
| `SERVER_A2S_ADDRESS`                   | `0.0.0.0`                                     | Address for A2S queries.                                                |
| `SERVER_A2S_PORT`                      | `17777`                                       | Port for A2S queries.                                                   |
| `RCON_ADDRESS`                         | `0.0.0.0`                                     | Address for RCON.                                                       |
| `RCON_PORT`                            | `19999`                                       | Port for RCON.                                                          |
| `RCON_PASSWORD`                        | (empty)                                       | Password for RCON. Required to enable RCON.                             |
| `RCON_PERMISSION`                      | `admin`                                       | Permission level for RCON clients.                                      |
| `GAME_NAME`                            | `Arma Reforger Docker Server`                 | Name of the server.                                                     |
| `GAME_PASSWORD`                        | (empty)                                       | Password required to join the server.                                   |
| `GAME_PASSWORD_ADMIN`                  | (empty)                                       | Admin password for the server.                                          |
| `GAME_ADMINS`                          | (empty)                                       | Comma-separated list of admin identity IDs or Steam IDs.                |
| `GAME_SCENARIO_ID`                     | `{ECC61978EDCC2B5A}Missions/23_Campaign.conf` | Scenario to load on the server.                                         |
| `GAME_MAX_PLAYERS`                     | `32`                                          | Maximum number of players allowed on the server.                        |
| `GAME_VISIBLE`                         | `true`                                        | Whether the server is visible in the server browser.                    |
| `GAME_SUPPORTED_PLATFORMS`             | `PLATFORM_PC,PLATFORM_XBL,PLATFORM_PSN`       | Platforms supported by the server.                                      |
| `GAME_PROPS_BATTLEYE`                  | `true`                                        | Whether BattlEye anti-cheat is enabled.                                 |
| `GAME_PROPS_DISABLE_THIRD_PERSON`      | `false`                                       | Whether third-person view is disabled.                                  |
| `GAME_PROPS_FAST_VALIDATION`           | `true`                                        | Whether fast validation is enabled.                                     |
| `GAME_PROPS_SERVER_MAX_VIEW_DISTANCE`  | `2500`                                        | Maximum view distance on the server.                                    |
| `GAME_PROPS_SERVER_MIN_GRASS_DISTANCE` | `50`                                          | Minimum grass rendering distance.                                       |
| `GAME_PROPS_NETWORK_VIEW_DISTANCE`     | `1000`                                        | Network view distance.                                                  |
| `GAME_MODS_IDS_LIST`                   | (empty)                                       | Comma-separated list of mod IDs with optional versions.                 |
| `GAME_MODS_JSON_FILE_PATH`             | (empty)                                       | Path to a JSON file containing mod definitions.                         |
| `SKIP_INSTALL`                         | `false`                                       | Whether to skip the installation of server files.                       |

> **Tip**: Check the [Dockerfile](Dockerfile#L32-L67) for additional details.

### Configs

By default, configs are generated from the environment variables. After the first run, you can expand the configuration file manually, but fields defined by environment variables will always be overwritten.

To use a custom configuration file, set the `ARMA_CONFIG` variable to a file in the `Configs` volume.

### Experimental Server

To use the experimental server, set the `STEAM_APPID` variable to `1890870`.

### Mods

Mods can be defined in three ways:

#### Using `GAME_MODS_IDS_LIST`

Provide a comma-separated list of mod IDs with optional versions:

```sh
-e GAME_MODS_IDS_LIST="5965770215E93269=1.0.6,5965550F24A0C152"
```

#### Using `GAME_MODS_JSON_FILE_PATH`

Provide a JSON file containing an array of mod objects:

```sh
-v ${PWD}/mods_file.json:/mods_file.json
-e GAME_MODS_JSON_FILE_PATH="/mods_file.json"
```

Example JSON:

```json
[
  {
    "modId": "597706449575D90B",
    "version": "1.1.1"
  }
]
```

#### Using Environment Variables in Docker Compose

You can define mods in your `docker-compose.yml` file, but you cannot use both `GAME_MODS_IDS_LIST` and `GAME_MODS_JSON_FILE_PATH` together. Below are two separate examples:

##### Example 1: Using `GAME_MODS_IDS_LIST`

```yaml
services:
  reforger-server:
    image: ghcr.io/acemod/arma-reforger:latest
    environment:
      - GAME_MODS_IDS_LIST=5965770215E93269=1.0.6,5965550F24A0C152
```

##### Example 2: Using `GAME_MODS_JSON_FILE_PATH`

```yaml
services:
  reforger-server:
    image: ghcr.io/acemod/arma-reforger:latest
    environment:
      - GAME_MODS_JSON_FILE_PATH=/mods_file.json
    volumes:
      - ./mods_file.json:/mods_file.json
```

### RCON

To enable RCON, define the `RCON_PASSWORD` variable:

```sh
-e RCON_PASSWORD="ExamplePassword123"
```

> **Important**: The password must:
> Be at least 3 characters long
> Not contain spaces

To change the [permission level](https://community.bistudio.com/wiki/Arma_Reforger:Server_Config#permission) for all RCON clients, use:

```sh
-e RCON_PERMISSION=""
```

---

## Running Multiple Server Instances

To run multiple instances of the server on the same machine, you need to increment the volumes and ports for each instance. Follow these steps:

### Step 1: Create Separate Volumes for Each Instance

Ensure each server instance has its own configuration, profile, and workshop directories. For example:

```bash
mkdir -p /path/to/instance1/configs /path/to/instance1/profiles /path/to/instance1/workshop
mkdir -p /path/to/instance2/configs /path/to/instance2/profiles /path/to/instance2/workshop
```

### Step 2: Increment the Ports for Each Instance

Each server instance must use unique ports. For example:

- Instance 1:
  - Bind Port: `2001`
  - Public Port: `2001`
  - A2S Port: `17777`
  - RCON Port: `19999`

- Instance 2:
  - Bind Port: `2002`
  - Public Port: `2002`
  - A2S Port: `17778`
  - RCON Port: `20000`

### Step 3: Run the Instances

#### Using Docker CLI

Run each instance with incremented ports and volumes:

```bash
docker create \
  --name=reforger-server-instance1 \
  -p 2001:2001/udp \
  -p 17777:17777/udp \
  -p 19999:19999 \
  -v /path/to/instance1/configs:/reforger/Configs \
  -v /path/to/instance1/profiles:/home/profile \
  -v /path/to/instance1/workshop:/reforger/workshop \
  -e SERVER_PUBLIC_PORT=2001 \
  ghcr.io/acemod/arma-reforger:latest
```

```bash
docker create \
  --name=reforger-server-instance2 \
  -p 2002:2002/udp \
  -p 17778:17778/udp \
  -p 20000:20000 \
  -v /path/to/instance2/configs:/reforger/Configs \
  -v /path/to/instance2/profiles:/home/profile \
  -v /path/to/instance2/workshop:/reforger/workshop \
  -e SERVER_PUBLIC_PORT=2002 \
  ghcr.io/acemod/arma-reforger:latest
```

#### Using Docker Compose

Define separate services for each instance in your `docker-compose.yml` file:

```yaml
version: "3.8"
services:
  reforger-server-instance1:
    image: ghcr.io/acemod/arma-reforger:latest
    ports:
      - "2001:2001/udp"
      - "17777:17777/udp"
      - "19999:19999"
    volumes:
      - /path/to/instance1/configs:/reforger/Configs
      - /path/to/instance1/profiles:/home/profile
      - /path/to/instance1/workshop:/reforger/workshop
    environment:
      - SERVER_PUBLIC_PORT=2001

  reforger-server-instance2:
    image: ghcr.io/acemod/arma-reforger:latest
    ports:
      - "2002:2002/udp"
      - "17778:17777/udp"
      - "20000:19999"
    volumes:
      - /path/to/instance2/configs:/reforger/Configs
      - /path/to/instance2/profiles:/home/profile
      - /path/to/instance2/workshop:/reforger/workshop
    environment:
      - SERVER_PUBLIC_PORT=2002
```

Start the instances with:

```bash
docker-compose up -d
````

### Note on Workshop Folder

To save download time, you can choose to use the same workshop folder on the host for multiple server instances. However, be aware that some mods may not function correctly with concurrent access. If you encounter issues, consider creating separate workshop folders for each instance.

---

By following these steps, you can run multiple server instances on the same machine while ensuring each instance operates independently.

## Using Environment Variables to Generate and Secure the Configuration

Follow these steps to generate the initial `docker_generated.json` configuration file using environment variables and ensure it remains secure from future changes.

---

### Step 1: Create and Configure the `.env` File

1. Ensure a `.env` file exists in the project directory. If not, create one.
2. Define the required environment variables in the `.env` file. For example:

  ```env
  GAME_NAME="My Secure Reforger Server"
  SERVER_PUBLIC_ADDRESS="your.public.ip"
  RCON_PASSWORD="SecurePassword123"
  ```

  > **Important**: Use a strong password for `RCON_PASSWORD` and avoid sharing the `.env` file publicly.

1. Save the `.env` file.

---

### Step 2: Generate the Initial Configuration File

1. Start the server using Docker Compose:

  ```bash
  docker-compose up -d
  ```

1. The server will generate a default configuration file (`docker_generated.json`) in the mounted `Configs` directory.

---

### Step 3: Secure the Configuration File

Locate the `docker_generated.json` file in the `Configs` directory:

  ```bash
  ./reforger/configs/docker_generated.json
  ```

Rename the file to prevent it from being overwritten by environment variable changes. For example:

  ```bash
  mv ./reforger/configs/docker_generated.json ./reforger/configs/my_secure_config.json
  ```

Update the `ARMA_CONFIG` environment variable in the `.env` file to point to the renamed configuration file:

  ```env
  ARMA_CONFIG=my_secure_config.json
  ```

---

### Step 4: Restart the Server

Restart the server to apply the changes:

  ```bash
  docker-compose down
  docker-compose up -d
  ```

Verify the server is running with the updated configuration by checking the logs:

  ```bash
  docker-compose logs -f
  ```

---

### Best Practices for Security

- **Environment Variables**: Keep sensitive information, such as passwords, in the `.env` file and avoid hardcoding them in the `docker-compose.yml` file.
- **File Permissions**: Restrict access to the `.env` file and configuration files to authorized users only.
- **Backups**: Regularly back up your configuration files to prevent data loss.

By following these steps, you can generate the initial configuration file securely and ensure it remains unaffected by future environment variable changes.

## Troubleshooting

### [Errno 2] No such file or directory: './ArmaReforgerServer'

#### Problem

The Python script `launch.py` is failing with a `FileNotFoundError` because the `ArmaReforgerServer` binary is missing. This typically occurs in a Docker environment where the necessary server files have not been properly installed or copied into the container image.

#### Solution

Ensure the Docker container is configured to download and install the server files. This is controlled by the `SKIP_INSTALL` environment variable.

1. **Set `SKIP_INSTALL` to `false`**

- **In a Docker Compose file:**
  Add or modify the environment variable under the `services` section:

  ```yaml
  services:
    arma-reforger-server:
      image: your-image-name
      environment:
        - SKIP_INSTALL=false
  ```

- **Using `docker run`:**
  Set the environment variable using the `-e` flag:

  ```bash
  docker run -e SKIP_INSTALL=false your-image-name
  ```

2. **Rebuild and Restart the Container**

- **With Docker Compose:** Run `docker-compose up --build`. The `--build` flag ensures the image is rebuilt with the updated environment variable.
- **With `docker run`:** Execute the updated `docker run` command. The container will start, and the installation script will download the `ArmaReforgerServer` binary and its dependencies.

> **Note**: If you use `docker-compose down` instead of `docker-compose stop`, the volume containing the installed files will be deleted. This means the server files will need to be reinstalled the next time the container is started. To avoid this, you can map a persistent volume for the installation files.

3. **Map a Persistent Volume for Installation Files**

To ensure the installation files persist even after running `docker-compose down`, map a volume for the installation directory. For example:

- **In a Docker Compose file:**

  ```yaml
  services:
    arma-reforger-server:
      image: your-image-name
      volumes:
        - /path/to/persistent/install:/reforger
      environment:
        - SKIP_INSTALL=false
  ```

- **Using `docker run`:**

  ```bash
  docker run -v /path/to/persistent/install:/path/to/install -e SKIP_INSTALL=false your-image-name
  ```

By mapping a persistent volume, the installation files will remain intact, and the server will not need to reinstall them after a `docker-compose down` or container removal.
