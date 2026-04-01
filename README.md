# Coredump Uploader

This utility can upload core dumps to Sentry.  It can either upload a single core dump or watch a folder
for incoming core dumps to automatically upload them as they happen.

## Requirements

- python
- poetry
- gdb
- elfutils

## Usage

```
upload_coredump [OPTIONS] PATH_TO_EXECUTABLE COMMAND [ARGS]...
```

### Global Options

These options apply to all commands and must be specified **before** the command name.

| Option | Required | Description |
|---|---|---|
| `PATH_TO_EXECUTABLE` | Yes | Positional argument. Path to the executable that generated the core dump. |
| `--sentry-dsn` | No | Your Sentry DSN. If not provided, the `SENTRY_DSN` environment variable is used. |
| `--gdb-path` | No | Path to the `gdb` binary. Defaults to `gdb` on `PATH`. |
| `--elfutils-path` | No | Path to the `eu-unstrip` binary (elfutils). Defaults to `eu-unstrip` on `PATH`. |
| `--all-threads` | No | Flag. When set, sends the backtrace from all threads to Sentry instead of only the crashed thread. |
| `--tag` / `-t` | No | Tag to attach to the Sentry event in `KEY:VALUE` format. Can be specified multiple times to add multiple tags. Tag keys and values must each be less than 200 characters. See [Sentry event payload docs](https://develop.sentry.dev/sdk/foundations/transport/event-payloads/) for details. |

### Commands

#### `upload`

Uploads a single core dump to Sentry.

```
upload_coredump [OPTIONS] PATH_TO_EXECUTABLE upload PATH_TO_CORE
```

| Argument | Required | Description |
|---|---|---|
| `PATH_TO_CORE` | Yes | Path to the core dump file to upload. |

**Examples:**

````
$ export SENTRY_DSN=https://something@your-sentry-dsn/42
$ upload_coredump /path/to/executable upload /path/to/core
````

````
$ upload_coredump --sentry-dsn https://something@your-sentry-dsn/42 /path/to/executable upload /path/to/core
````

````
$ upload_coredump --all-threads --sentry-dsn https://something@your-sentry-dsn/42 /path/to/executable upload /path/to/core
````

````
$ upload_coredump --tag environment:production --tag service:myapp /path/to/executable upload /path/to/core
````

#### `watch`

Watches a directory for new core dumps and automatically uploads them as they appear. Files matching the pattern `.*core.*` are detected.

```
upload_coredump [OPTIONS] PATH_TO_EXECUTABLE watch WATCH_DIR
```

| Argument | Required | Description |
|---|---|---|
| `WATCH_DIR` | Yes | Path to the directory to watch for incoming core dumps. |

**Example:**

````
$ upload_coredump --sentry-dsn https://something@your-sentry-dsn/42 /path/to/executable watch /path/to/dir
````

## Development

We use Poetry for development. To get started, first install dependencies: 

```
poetry install
```

To run tests, use:

```
poetry run pytest tests/
```

To run the application:

```
poetry run upload_coredump ...
```
