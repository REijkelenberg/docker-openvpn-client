# Docker OpenVPN Client
![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/reijkelenberg/openvpn-client.svg)
![License MIT](https://img.shields.io/badge/license-MIT-blue.svg)

This is a fork of [ekristen/docker-openvpn-client](https://github.com/ekristen/docker-openvpn-client), which (I think) is not being maintained anymore. At the moment, the code in this repo does not contain any changes, except for an updated version of the readme below. In the future, I might modify the image to prevent e.g. DNS leaks and to include a fail-safe switch.

## How to

1. In case the OpenVPN server uses user authentication, add the following line to the OpenVPN client config file. Additionally, place your username on the first line of `user-auth.pwd` and place the corresponding password on the second line of `user-auth.pwd`:
```
auth-user-pass /vpn/user-auth.pwd
```
2. In case the private key is password protected, add the password for the private key in your OpenVPN client config file to `key.pwd`
3. Add the generated OpenVPN client config to a directory. Call it e.g. `client.ovpn`.
4. The following command can be used to start the container. Depending on which passwords you need to provide, some options may not be required and will throw an error. Depending, again, on your use case, map `user-auth.pwd`, `key.pwd`, and (obviously) `client.ovpn` to the Docker container.
```
docker run -d --name vpn-client \
  --cap-add=NET_ADMIN \
  --device /dev/net/tun \
  --dns <dns.of.openvpn.server>
  -v /path/with/vpn/configs:/vpn \
  reijkelenberg/openvpn-client --config /vpn/client.ovpn --askpass /vpn/key.pwd
```

### Route other container traffic through OpenVPN

Use `--net=container:<container-name>` on the containers that you want to make user of the OpenVPN client. All traffic of these containers will be routed through the OpenVPN client container. Example:

```
docker run -it --rm \
  --net=container:vpn-client
  ubuntu /bin/bash
```
