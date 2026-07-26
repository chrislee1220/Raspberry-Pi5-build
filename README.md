# OpenWrt CM5 + Chinese + HomeProxy

This repository builds an OpenWrt 25.12.5 image for Raspberry Pi Compute
Module 5 from the official OpenWrt SDK and ImageBuilder.

## 1. Create the GitHub repository

1. Create an empty GitHub repository, for example `openwrt-cm5-homeproxy`.
2. Upload all files in this directory, including `.github`.
3. Open the repository's **Actions** page and enable workflows if prompted.
4. Select **Build OpenWrt for Raspberry Pi CM5** and click **Run workflow**.
5. Wait for the `build` job to finish, then download the artifact from the
   bottom of the workflow run page.

No GitHub token, repository secret, or self-hosted runner is required.

Alternatively, push the template from a terminal:

```sh
git init
git add .
git commit -m "Add CM5 OpenWrt builder"
git branch -M main
git remote add origin https://github.com/YOUR_NAME/openwrt-cm5-homeproxy.git
git push -u origin main
```

## 2. Select the correct image

For a first installation to an SD card or CM5 eMMC, use:

```text
openwrt-25.12.5-bcm27xx-bcm2712-rpi-5-squashfs-factory.img.gz
```

For a later upgrade from a running OpenWrt installation, use:

```text
openwrt-25.12.5-bcm27xx-bcm2712-rpi-5-squashfs-sysupgrade.img.gz
```

Do not write the `sysupgrade` image to a blank SD card or eMMC.

## 3. First installation

### SD card

Use Raspberry Pi Imager or balenaEtcher to write the compressed `factory`
image directly to the SD card.

### CM5 eMMC

1. Put the CM5 carrier board into USB boot mode.
2. Connect its USB device/boot port to the computer.
3. Run Raspberry Pi `rpiboot` so the eMMC appears as a removable drive.
4. Write the `factory` image with Raspberry Pi Imager or balenaEtcher.
5. Power off, disable USB boot mode, and boot the CM5 normally.

The image checksum is stored in `SHA256SUMS` inside the downloaded artifact.

## 4. First login

The default LAN address is normally:

```text
http://192.168.1.1
```

The initial user is `root` and the official image initially has no password.
Connect from the LAN side, set a strong root password immediately, and do not
expose LuCI or SSH to the WAN.

The system timezone and LuCI language are preset to `Asia/Shanghai` and
Simplified Chinese. HomeProxy is installed but intentionally not enabled or
preconfigured.

## 5. Configure WAN and LAN

The workflow does not hard-code interface assignments. CM5 carrier boards and
USB/PCIe network adapters expose different interface names.

After first boot, identify interfaces with:

```sh
ip -br link
```

Keep the working management port as LAN, then assign the second physical port
to WAN in **Network > Interfaces**. Confirm local access still works before
enabling HomeProxy.

## 6. Upgrade policy

OpenWrt, SDK, ImageBuilder and kernel packages must always use exactly the same
release. To upgrade, update these values together in the workflow:

- `OPENWRT_VERSION`
- SDK filename when the GCC version changes
- `IMAGEBUILDER_SHA256`
- `SDK_SHA256`

Update `HOMEPROXY_REF` only after checking the selected commit against that
OpenWrt release. Never mix release packages with snapshot packages.

When upgrading from LuCI, upload the generated `sysupgrade` image. Back up the
configuration first. For a major OpenWrt release upgrade, do not preserve old
configuration unless it has already been tested on spare media.

## Sources

- OpenWrt target files: https://downloads.openwrt.org/releases/25.12.5/targets/bcm27xx/bcm2712/
- OpenWrt CM5 device definition: https://github.com/openwrt/openwrt/blob/v25.12.5/target/linux/bcm27xx/image/Makefile
- HomeProxy: https://github.com/immortalwrt/homeproxy
