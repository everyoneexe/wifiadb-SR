# WiFiADB-SR

> **Note:** This repository is a fork of [mrh929/magisk-wifiadb](https://github.com/mrh929/magisk-wifiadb) with dedicated **Soft Reboot / Soft Restart (SR)** support, inspired by the stale-daemon handling approach in [Vector-SR](https://github.com/byemaxx/Vector-SR).

WiFiADB-SR is a Magisk / KernelSU / APatch module that adds persistent Wireless (WiFi) Debugging to Android Stock and Custom ROMs, enhanced with full protection against soft-reboot performance degradation.

## Key Improvements in WiFiADB-SR

- **Soft Reboot (SR) Awareness:** The background watchdog continuously monitors `sys.boot_completed` during runtime. If Android runtime / Zygote restarts (soft reboot), the script pauses immediately and avoids restarting `adbd` while framework is recovering, preventing race conditions.
- **Stale Daemon Prevention (Vector-SR approach):** Uses a PID tracking mechanism (`/data/local/tmp/magisk_wifiadb.pid`) to detect and terminate any hanging or orphaned background shell instances from previous runs or soft reboots.
- **ZRAM & CPU Thrashing Protection:** Polling frequency is optimized (5s default) to eliminate rapid subshell forking, preventing aggressive swapping to ZRAM and CPU throttling.
- **Clean Exit:** Implements signal traps (`INT`, `TERM`, `HUP`, `EXIT`) to clean up lockfiles upon termination.

---

## Features

- Configure WiFi ADB to activate on boot and survive soft reboots.
- Toggle WiFi ADB ON/OFF using the Magisk / KernelSU Manager app.
- Set your own private custom port for your device.
- Enable logging to view the WiFi ADB status.

## Usage

**Configuring WiFi ADB to start on boot:**

1. Download the latest release of the module.
2. Manually install it using the Magisk / KernelSU Manager app.
3. Enable the WiFi ADB module.
4. Reboot your device.
5. Your wireless debugging is now enabled.

**Commands to establish a wireless ADB connection:**

Connect using the command:
```bash
adb connect your-device-IP:1234
```
Once connected, all other ADB commands will work as expected.

Disconnect using the command:
```bash
adb disconnect
```

**Toggling WiFi ADB ON/OFF:**
Open your Root Manager app -> Modules

Use the module switch to enable/disable WiFi ADB.

## Configuration

You can configure module settings by creating a `config` file in the module root directory (usually located at `/data/adb/modules/wifiadb-sr`) with the following lines:
```
ADB_PORT=1234
ENABLE_LOG=1
STATUS_CHK_FREQUENCY=5
```

**Supported configurations:**

| Name                   | Default | Comment |
|------------------------|---------|---------|
| ENABLE_LOG             | 0       | 1 for ON / 0 for OFF |
| ADB_PORT               | 5555    | Your custom ADB port number (1~65535) |
| STATUS_CHK_FREQUENCY   | 5       | Status check interval in seconds (prevents CPU/ZRAM thrashing) |

**Commands to add the above lines to the config file:**
```bash
adb shell
su
cd /data/adb/modules/wifiadb-sr/
echo -e "ADB_PORT=1234\nENABLE_LOG=1\nSTATUS_CHK_FREQUENCY=5" > config
```

## Logs

If logging is enabled, logs can be viewed at `/data/local/tmp/wifiadb.log`.

## Credits

- Forked from original [mrh929/magisk-wifiadb](https://github.com/mrh929/magisk-wifiadb).
- Soft-Restart recovery and stale daemon mitigation concepts inspired by [Vector-SR](https://github.com/byemaxx/Vector-SR).
