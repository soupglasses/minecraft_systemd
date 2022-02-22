# ansible-role-minecraft-systemd

Ansible role to set up minecraft servers and run them trough systemd.

## Quick setup:

Example, make a basic fabric server and add a mod to it:
```yaml
roles:
  - role: minecraft
    vars:
      minecraft_fabric_enable: true

      minecraft_fabric_version: 1.18.1
      minecraft_fabric_loader_version: 0.13.1
      minecraft_fabric_installer_version: 0.10.2

      minecraft_prop_difficulty: hard
      minecraft_prop_server_port: "25565"

      minecraft_min_ram: 2G
      minecraft_max_ram: 2G
      
      minecraft_fabric_mods:
        - url: https://github.com/FabricMC/fabric/releases/download/0.46.4%2B1.18/fabric-api-0.46.4+1.18.jar
          name: fabric-api-0.46.4+1.18.jar
          checksum: 'sha256:bbc1ac58aabdc735208a2654857e838312aa87ee3e94178cc7790b382c8355a3'
```

Remember to open the port if you have any firewalls enabled.

If you need to input a command manually to the running server. Run `echo '[your-command]' > /run/minecraft-[your-server-name]/console`.

## Configuring:

### Common Settings:
- `minecraft_server_name`: Your instance name, will be what the systemd and folder will be named. Defaults to `default`.
- `minecraft_min_ram`/`minecraft_max_ram`: Set as `1000M` or `1G`. Reccomended both be put as the same value.

### Dealing with server.properties:
Every setting in the server properties is defined in the `20-properties.yml` file. You can set any of the settings with `minecraft_prop_` + settnig.
NOTE: Dashes and dots need to replaced with underscores.
Example: To change `query.port` you set `minecraft_prop_query_port`.

### Advanced Settings:
- `minecraft_java_flags`: Additional java flags to pass in. 


## Backends:

### Fabric:
To use fabric, enable it with: `minecraft_fabric_enable: true`

To set the version to download and use, set the `minecraft_fabric_version`, `minecraft_fabric_loader_version`, `minecraft_fabric_installer_version`, and `minecraft_fabric_checksum`. Theese options can be found on Fabric's own websites under the Server download page.

NOTE: You need to set all four, you cant simply change the minecraft version. It might not exist with the defined loader and installer versions!

Example:
```yaml
minecraft_fabric_version: 1.18.1
minecraft_fabric_loader_version: 0.13.1
minecraft_fabric_installer_version: 0.10.2
minecraft_fabric_checksum: 'sha256:bb30699ddc27bfa1b9dec48398a642605a69068828b4a46de6b9dfc952d4cda2'
```

To install any mods, use the `minecraft_fabric_mods` setting. You need to define a URL, name of thedownloaded jar, and its checksum. (You can find the checksum by running `sha256sum filename.jar`).

Example:
```yaml
minecraft_fabric_mods:
  - url: https://github.com/FabricMC/fabric/releases/download/0.46.4%2B1.18/fabric-api-0.46.4+1.18.jar
    name: fabric-api-0.46.4+1.18.jar
    checksum: 'sha256:bbc1ac58aabdc735208a2654857e838312aa87ee3e94178cc7790b382c8355a3'
```

### What about X server?

Feel free to help me out by opening an issue, a pr, or similar. I only needed this ansible role to support my usecase of fabric. But i wrote it in a way where you can put in any backend, even manual.

If you have a jar file you would like to use. You can symlink or move it to `/opt/minecraft/worlds/your-instance/server.jar` outside of this ansible script.

## Local Dev Environment

First setup:
1. `python -m venv venv`
2. `source venv/bin/activate`
3. `python -m pip install --upgrade pip wheel setuptools`
4. `python -m pip install -r requirements.txt`
5. `pre-commit install`

Using it after first setup:
1. `source venv/bin/activate`

### Tip for dealing with properties file.

Each time the service boots, it will recursively look for `*.ansible` files, and make a copy without the extention. This is extremely handy to work with the horrible `server.properties` file. Which are parsed and completely rewritten each time they are read.
