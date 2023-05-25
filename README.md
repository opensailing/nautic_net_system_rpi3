# NauticNet Raspberry Pi 3 System Image

This image is forked from the [base system image](https://github.com/nerves-project/nerves_system_rpi3) and adds support for the PiCAN-M hat by adding MCP2515 device drivers and the `socketcand` package to the base image.

## Building This Image

This guide is based on [the docs](https://hexdocs.pm/nerves/customizing-systems.html). Building the system image should only be required when making changes to the system repo, either to upgrade the base version by merging in upstream changes, or just to add/change existing functionality.

### Prerequisites

- macOS: Docker installed and running

### macOS Preparation

APFS on macOS is case-insensitive by default. This becomes a problem when trying to build the Linux kernel because it expects case-sensitivity. To mitigate this:

1. In Disk Utility, create a new Volume, name it "Nerves", and select the format "APFS (Case-sensitive)".
2. Add the following environment variables:
   ```sh
   export NERVES_DL_DIR='/Volumes/Nerves/dl'
   export NERVES_ARTIFACTS_DIR='/Volumes/Nerves/artifacts'
   ```

### Building the Image Locally

In this repo:

```sh
# This should fail to find a prebuilt image
mix deps.get

# This will take a long time on the first run - about one hour. It is
# much faster when making incremental changes.
mix compile
```

Note that you must recompile when making ANY changes to this repo! Even just this README.

Now, back in the `nautic_net_device` app, make sure the dep is pointing to the local folder, instead of the web:

```
# mix.exs
{:nautic_net_system_rpi3,
  path: "../../../nautic_net_system_rpi3", runtime: false, targets: :nautic_net_rpi3},
```

When running `mix deps.get` it should successfully find the local system artifact:

```
Checking for prebuilt Nerves artifacts...
  Found nautic_net_system_rpi3 in cache
    /Volumes/Nerves/artifacts/nautic_net_system_rpi3-portable-1.20.1
```

### Publishing the Image Artifact to GitHub

When you are ready to :shipit:

```sh
mix nerves.artifact
```

Tag the commit with the version number, and push the tag to GitHub. Then upload the generated .tar.gz file to the GitHub release for that tag at: https://github.com/DockYard/nautic_net_system_rpi3/releases.
