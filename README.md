# AlususNet

Client library for Alusus Net platform - a hosting and deployment service for Alusus applications.

## Overview

AlususNet provides a comprehensive client library and CLI tool for deploying Alusus projects to the Alusus Net hosting platform. It handles project publishing, database backup, and restoration operations with secure authentication and interactive setup.

## Installation

Import the library in your Alusus project:

```alusus
import "Apm";
Apm.importPackage("Alusus/AlususNet@0.2");
```

## Library API

### Client Class

The `Client` class provides programmatic access to Alusus Net services.

#### Instantiation

The Client class can be instantiated in several ways:

```alusus
// Basic instantiation with project name only
def client: SrdRef[AlususNet.Client] = AlususNet.Client("my-project");

// Instantiation with API token (useful for CI/CD)
def client: SrdRef[AlususNet.Client] = AlususNet.Client("my-project", "api-token-here");

// Instantiation with verbose mode control
def client: SrdRef[AlususNet.Client] = AlususNet.Client("my-project", "api-token", true);
```

**Parameters:**
- `projectName` (String): The name of your project on Alusus Net. Must contain only lowercase letters, numbers, and hyphens, and must not start or end with a hyphen.
- `apiKey` (String, optional): Your Alusus Net API token. If not provided, you'll be prompted for login credentials.
- `verbose` (bool, optional): Enable verbose HTTP request logging. Default: `false`.

#### publish Method

Publishes your project to Alusus Net, making it available on the platform.

```alusus
// Simple publish with server port
client.publish(8080);

// Publish with custom project path and starter executable
client.publish(8080, "/path/to/project", "my-app");
```

**Parameters:**
- `serverPort` (Int): The HTTP port on which your application will listen.
- `projectPath` (String, optional): Path to your project directory. If not provided, uses the current directory.
- `starter` (String, optional): Name of the executable file that starts your application.

**Behavior:**
- Validates project name format
- Fetches available hosting products
- Authenticates with Alusus Net (if needed)
- Sets up project and payment plan (interactive)
- Creates and uploads a tarball of your project
- Displays upload progress

#### restoreDbBackup Method

Uploads and restores a PostgreSQL database backup to your project's container.

```alusus
client.restoreDbBackup("/path/to/backup.sql");
```

**Parameters:**
- `restore_db_path` (String): Path to the PostgreSQL backup file to upload.

**Behavior:**
- Authenticates with Alusus Net
- Uploads the backup file in chunks
- Displays upload progress
- Restores the database on the server

#### downloadDbBackup Method

Creates and downloads a backup of your project's PostgreSQL database.

```alusus
client.downloadDbBackup("/path/to/save/backup.sql");
```

**Parameters:**
- `backup_db_path` (String): Path where the backup file should be saved.

**Behavior:**
- Authenticates with Alusus Net
- Requests database backup creation
- Waits for backup to be ready
- Downloads the backup file
- Saves it to the specified path

## CLI Tool

AlususNet includes a command-line interface for interacting with the platform.

### Building the CLI

To build the CLI tool:

```bash
alusus cli.alusus build-cli
```

This creates an executable at `./Build/alusus-net`.

### CLI Commands

#### publish

Publish your project to the Alusus Net platform.

```bash
alusus-net publish <projectName> <projectPath> <port> [starter] [apiToken]
```

**Arguments:**
- `projectName`: The name of the project on Alusus Net
- `projectPath`: Path to the project folder
- `port`: The HTTP port on which the app will be listening
- `starter` (optional): The name of the executable file of the project
- `apiToken` (optional): Alusus Net API token. If not provided, you'll be prompted for login

**Example:**
```bash
alusus-net publish my-app ./my-project 8080 server
```

#### backup-db

Backup the database of your container and download it.

```bash
alusus-net backup-db <projectName> <backupPath> [apiToken]
```

**Arguments:**
- `projectName`: The name of the project on Alusus Net
- `backupPath`: The path to save the PostgreSQL backup file to
- `apiToken` (optional): Alusus Net API token. If not provided, you'll be prompted for login

**Example:**
```bash
alusus-net backup-db my-app ./backup.sql
```

#### restore-db

Upload and restore a database to your container.

```bash
alusus-net restore-db <projectName> <backupPath> [apiToken]
```

**Arguments:**
- `projectName`: The name of the project on Alusus Net
- `backupPath`: The path to the PostgreSQL backup file to upload
- `apiToken` (optional): Alusus Net API token. If not provided, you'll be prompted for login

**Example:**
```bash
alusus-net restore-db my-app ./backup.sql
```

## Authentication

AlususNet supports multiple authentication methods:

1. **Interactive Login**: Enter username and password when prompted
2. **Multi-Factor Authentication (MFA)**: Supported with trust tokens for subsequent logins
3. **API Tokens**: Pass an API token for non-interactive use (e.g., CI/CD pipelines)

Authentication credentials are stored in `~/.config/alususnet.conf` by default. You can override this location using the `ALUSUS_NET_CONFIG_PATH` environment variable.

## Environment Variables

- `ALUSUS_NET_HOST`: Override the default Alusus Net host (default: `https://alusus.net`)
- `ALUSUS_NET_CONFIG_PATH`: Override the default configuration file path (default: `~/.config/alususnet.conf`)

## Project Name Requirements

Project names must:
- Contain only lowercase letters (a-z), numbers (0-9), and hyphens (-)
- Not start or end with a hyphen
- Match the pattern: `^[a-z0-9\-]+$`

## License

This project is licensed under the GNU Lesser General Public License v3.0 (LGPL-3.0). See the `COPYING` and `COPYING.LESSER` files for details.

## Support

For issues, questions, or support, please contact Alusus Net support or visit https://alusus.net

## Terms of Service

By using Alusus Net, you agree to:
- Terms of Service: https://alusus.net/terms-of-service
- Privacy Policy: https://alusus.net/privacy-policy

You will be prompted to accept these terms when first publishing a project.

