# Sagittarius

Docker Compose stacks to be deployed to Sagittarius through Portainer's GitOps feature.

## Architecture

### Networking

For reasons of security, the stacks here don't just expose their ports willy-nilly. Instead, most connections are proxied through [Traefik](https://traefik.io), which handles things like middlewares, access control and TLS.

By default, Docker Compose creates a bridge network for each stack, preventing containers from interacting with each other across stacks. Traefik needs to be able to do this, so the stack relies on an **externally-created** network called `traefik_bridge`, that connects proxied containers together. Some stacks have sidecar containers (like databases) that are not connected to this network, but are instead connected to the main container through the stack's default network. This allows the primary container to use the sidecar container, without the sidecar being directly exposed to the rest of the network (and potentially the outside world).

Where Traefik is simply not equipped to proxy a container, a port can be exposed and used as normal. Examples of where this is used include:

- [Gitea](https://gitea.com): SSH (port 22 over TCP)
- [Adguard Home](https://adguard.com/en/adguard-home/overview.html): DNS (port 53 over TCP/UDP)
- [LLDAP](https://github.com/lldap/lldap): LDAP (port 389 over TCP)
- [mc-router](https://github.com/itzg/mc-router): Minecraft (port 25565 over TCP)
- [WG Easy](https://github.com/wg-easy/wg-easy): Wireguard (port 51820 over UDP)
