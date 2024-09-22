
# esp-idf-dev-container

The following guide explains how mount an ESP32 (devboard) for usage inside this VSCode dev container. 

## Prerequisites

- Windows 10 / 11
- WSL2
- Docker Desktop
- `usbipd` (install via `winget install usbipd`)

## Steps

### 1. Forward the USB Device to WSL

When running Docker on Windows with WSL 2, you'll need to forward the USB device to your WSL distribution using `usbipd`. Here's how you do it:

1. List all available USB devices on Windows:

    ```bash
    usbipd list
    ```

    You should see a list of devices connected to your system, along with their BusID (e.g., `1-1`). Your ESP32 should look something like this: <br> `9-3; 10c4:ea60  Silicon Labs CP210x USB to UART Bridge (COM3); Not shared`
<br>
2. Register the device for sharing:

    ```bash
    usbipd bind --busid 9-3
    ```
    Replace `<BusID>` with the BusID of your USB device.
<br>
3. Attach the USB device to your WSL 2 distribution:

    ```bash
    usbipd attach --wsl  --busid <BusID>
    ```

    Replace `<BusID>` with the BusID of your USB device. Optionally you can the specify the distribution to witch the USB device should be attached with ` --distribution <distribution-name>`. This is not requiered since the system will pick one if not specified. The `<distribution-name>` would be the WSL2 distro name (e.g. `ubuntu`, `docker-desktop`). If the USB device should be automatically re-attach when the device is detached or unplugged use `-a`.


### 2. Identify the USB Device in WSL

Once the USB device is attached to your WSL 2 distribution, identify the device in WSL. You can list all connected devices with the following command:

```bash
dmesg | grep tty
```

Look for a line mentioning your device (e.g., `cp210x converter now attached to ttyUSB0`). This will be the device path you will use to mount the USB device inside the container.

### 3. Edit the dev container config

Inside the [docker-compose.yml](.devcontainer/docker-compose.yml) file look for the parameter line `- devices`. Make sure you mount your device there to make it available inside the dev container. It is possible to add multiple devices to your container.
Here’s on how to do that (e.g. for root path = `/dev/ttyUSB0`)

```bash
"/dev/ttyUSB0:/dev/ttyUSB0"
```

### 4. Verify the Device is Mounted

Once inside the container, verify that the device is correctly mounted by listing the `/dev` directory:

```bash
ls -l /dev/ttyUSB0
```

If the device is mounted, you should see an output like this:

```bash
crw-rw----    1 root     dialout   188,   0 Sep 21 12:00 /dev/ttyUSB0
```

This confirms that the device is available inside the container.




## Notes

- Make sure the USB device is properly connected to your host system before starting the container.
- **make sure in the windows device manager that windows has fond the appropriet firmware for your devie**
- The path `/dev/ttyUSB0` may vary depending on the specific USB device you are using. Adjust accordingly.
