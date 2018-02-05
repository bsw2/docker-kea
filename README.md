# Kea
docker images for running ISC Kea


These images are built using packages that aren't in Alpine yet.  I've built
them  from [poppypop](https://hub.docker.com/u/poppypop/)'s fork of aport.
See: https://github.com/alpinelinux/aports/pull/3007

I'm building my images with one service per container.  If you prefer to
have multiple related services running in one container you should probably
take a look at [poppypop/docker-kea](https://hub.docker.com/r/poppypop/docker-kea/)

# Examples
```bash
docker run --rm --name kea -v kea_dhcp4_config:/etc/kea -v kea_data:/var/kea/kea --net="host" bsw2/kea-dhcp4
docker run --rm --name control -p 80:8080 -v kea_ctrlagent_config:/etc/kea -v kea_data:/var/kea/kea bsw2/kea-ctrl-agent
```

# to change a running dhcp4 config
## retrieve the config

```bash
curl -X POST -H "Content-Type: application/json" -d '{ "command": "config-get", "service": [ "dhcp4" ] }' http://localhost/ |jq '{ "command": "config-set", "service": [ "dhcp4" ], "arguments": .[0].arguments }' >update.json
```

## make your changes
edit update.json

## upload your new config, dhcp4 will now run with this config
```bash
curl -X POST -H "Content-Type: application/json" -d @update.json http://localhost/
```

## write your new config to disk
```bash
curl -X POST -H "Content-Type: application/json" -d '{ "command": "config-write", "service": [ "dhcp4" ]}' http://localhost/
```
