# 2 - Build

## Build Script
- S++ uses a `build.toml` file for build configuration.
- All `mod` tagged `.spp` files in the `src` directory are compiled.
- The entry point is `src/main.spp. fn main() -> Void { }`.

## Commands
#### `spp build`
- Standard build command for an s++ project (debug or release).
- Creates a `bin` directory next to the `src` directory.
- All binary/metadata files are placed in this `bin` directory.
- Does not run the binary after building.

| Flag             | Mutually Exclusive | Description                                |
|------------------|--------------------|--------------------------------------------|
| `-r`/`--release` | `-d`               | Build in release mode.                     |
| `-d`/`--debug`   | `-r`               | Build in debug mode.                       |
| `-c`/`--clean`   |                    | Clean the build directory before building. |
| `-h`/`--help`    |                    | Show help.                                 |


#### `spp run`
- Runs the binary files stored in the `bin` directory.
- The project must be built first: `spp run` does not build the project.
- No mode needs to be specified, as the binary files are already built.

| Flag          | Mutually Exclusive | Description |
|---------------|--------------------|-------------|
| `-h`/`--help` |                    | Show help.  |

#### `spp buildrun`
- Builds the project, then runs it.
- Calls `spp build [flags]`, then `spp run`.
- The flags are passed to `spp build`.

| Flag             | Mutually Exclusive | Description                                |
|------------------|--------------------|--------------------------------------------|
| `-r`/`--release` | `-d`               | Build in release mode.                     |
| `-d`/`--debug`   | `-r`               | Build in debug mode.                       |
| `-c`/`--clean`   |                    | Clean the build directory before building. |
| `-h`/`--help`    |                    | Show help.                                 |

#### `spp clean`
- Cleans the `bin` directory, but doesn't delete the folder.

| Flag          | Mutually Exclusive | Description |
|---------------|--------------------|-------------|
| `-h`/`--help` |                    | Show help.  |

#### `spp help`
- Shows help.
- Shows a list of commands, and their descriptions.

#### `spp version`
- Shows the version.

## Build Configuration
- The `build.toml` file is used for build configuration.
- The `build.toml` file is a single file, and is located in the root directory of the project.

### Core Attributes
```toml
[project]
name = "My Project"
version = "0.1.0"
authors = ["SamG101"]
license = "MIT"
```

### Include 3rd party projects
```toml
[vcs]
spp_compiler_4 = { vcs: github, url: "SamG101-Developer/SPP-Compiler-4", auto_fetch: true }
spp_4 = { vcs: github, url: "SamG101-Developer/SPP-4", auto_fetch: true }
```
- Each project is scanned to make sure it is a valid S++ project, and the name matches.
- The project is then downloaded and placed in the `src` directory.
- The flags for vcs are:
    - `vcs`: The version control system to use. Currently only `GitHub` is supported.
    - `url`: The url of the project, from the user/organisation to the project name.
    - `version`: The branch of the project to use. Defaults to `master`.
    - `auto_fetch`: Whether to automatically fetch the project when building. Defaults to `true`.
