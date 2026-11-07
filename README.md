# hmgapi-adb

**ADB**, a mod for [HMG-API](https://github.com/Open-HMG/HMG-API) on Hyundai, Kia and
Genesis Gen5W head units: adb over USB and over the car's hotspot (port 5555), each on
or off, applied right away.

It replaces the Engineering Mode ADB TCP switch (`persist.service.adb.tcp.enable`). That
switch gives `eth0` an address on the hotspot's subnet, so phones that join the hotspot
get no address and wireless CarPlay and Android Auto fail. The mod holds the switch at
0 and opens adb over the network with `persist.adb.tcp.port` instead.

## Settings

| Setting | Default | What |
|---|---|---|
| ADB over USB | on | the USB port in device mode for a computer (`persist.sys.usb.config=adb`). While it's on, the port takes no phones (wired CarPlay/Android Auto) or USB drives, a HMG-API rescue stick included. Off: `host,adb`, the stock host mode. |
| ADB over Wi-Fi | off | `adb connect 192.168.0.1:5555` from a computer on the car's hotspot. Anyone on the hotspot can use it: this head unit's adb doesn't check keys. |

Changes apply right away (adbd restarts). Removing the mod puts the stock values back.

## Cars

Gen5W head units on Telechips TCC803x (`mod.json` `"system"`), with HMG-API 1.0.0 or
newer. HMG-API's [Supported cars](https://github.com/Open-HMG/HMG-API/wiki/Supported-cars)
lists the cars.

## Installing

From the HMG-API app (USB stick, `HMGAPI/updates/`), NEMESIS or adb: see
[Installing and removing mods](https://github.com/Open-HMG/HMG-API/wiki/Installing-and-removing-mods).
Until this repo's first release (after HMG-API 1.0.3), the package is the one on
[HMG-API v1.0.2](https://github.com/Open-HMG/HMG-API/releases/tag/v1.0.2):
`adb-1.0.0.zip`, or `adb-1.0.0-nemesis.zip` for NEMESIS.

The NEMESIS package refuses NEMESIS's own ADB over TCP package (`adb.tcp`), which
changes the same switch (`mod.json` `"nemesis_conflicts"`).

## Building and testing

```
adb/               the mod: mod.json, patches.json, settings.json; test.sh (off-car test)
hmgapi-sdk.json    the HMG-API mod SDK it builds with (a HMG-API commit until 1.0.3)
.github/workflows  ci.yml (HMG-API's mod workflow), release.yml
```

With the SDK unpacked in `$HMGAPI_SDK`:

```
HMGAPI_SDK=$HMGAPI_SDK sh adb/test.sh
```

```
$HMGAPI_SDK/bin/mkmodpkg.sh adb <key>
```

How mods build outside HMG-API: HMG-API's `docs/mod-reference.md`, "Building a mod
outside this repo". This mod uses no hook points (props only); a change that needs one
starts as a HMG-API pull request (`docs/mod-repos.md`, section 4).

## Releases

A tag `v<version>`, the same as `adb/mod.json`'s `"version"`, runs `release.yml`: it
builds and tests the mod, signs it with the author key (the `release` environment's
`MOD_KEY`, the key installed cars have) and makes a **draft** release with the package
and its NEMESIS package. A maintainer publishes it, then adds it to HMG-API's catalogue
by pull request with the `tools/catalog.py add` command the run prints.

No releases until HMG-API 1.0.3.

## Licence

AGPL-3.0 ([LICENSE](LICENSE)).
