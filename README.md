# Hashicorp Vault auto-initializer image

Vault with auto-initializer. Creating KV engine, KV storage and KV secrets from environment variables.
**ONLY FOR DEV USAGE**.

## Table of content

- [Key features](#key-features)
- [How to use](#how-to-use)
- [Todo](#todo)
- [Author](#author)
- [License](#license)

## Key features
- Launching Vault server in development mode (Vault configuration and secrets are created at every startup and not
  persisted on hard drive).
- By default root token is set as `admin`.
- You can provided one KV engine name (by default it is `dev`) and assign multiple KV storages.
- Into KV storages you can assign multiples KV secrets defined as key-value environment variable.

## How to use

- standalone via `docker` command (with example KV secrets):

```bash
$ docker run -d \
  -p 8200:8200 \
  --cap-add IPC_LOCK \
  -e VAULT_AUTO_INIT_SERVER_PORT=<optional, by default 8200> \
  -e VAULT_AUTO_INIT_ROOT_TOKEN=<optional, by default 'admin'> \
  -e VAULT_AUTO_INIT_KV_ENGINE=<optional, by default 'dev'> \
  -e KV_STORAGE_0=service1 \
  -e KV_STORAGE_1=service2 \
  -e V_SECRET_TOKEN_0=SuperSecretToken0 \
  -e V_SECRET_TOKEN_1=SuperSecretToken1 \
  milosz08/vault-dev:latest
```
- via `docker compose` file (with example KV secrets):

```yml
services:
  vault-dev:
    container_name: vault-dev
    image: milosz08/vault-dev:latest
    ports:
      - '8200:8200'
    environment:
      # server config
      VAULT_AUTO_INIT_SERVER_PORT: # <optional, by default: 8200>
      VAULT_AUTO_INIT_ROOT_TOKEN: # <optional, by default: 'admin'>
      VAULT_AUTO_INIT_KV_ENGINE: # <optional, by default: 'dev'>
      # kv storages
      KV_STORAGE_0: core
      KV_STORAGE_1: common
      # kv secrets
      V_SECRET_TOKEN_0: SuperSecretToken0
      V_SECRET_TOKEN_1: SuperSecretToken1
    cap_add:
      - IPC_LOCK
    networks:
      - my-infra-network

# other containers...

networks:
  my-infra-network:
    driver: bridge
```

### How define new KV storages and secrets?

- Every KV storage variable key must be defined as `KV_STORAGE_[X]`, where `[X]` is the storage identifier (as number).

- Every KV secret variable key must be defined as `V_[Y]_[X]`, where `[X]` is the KV storage identifier and `[Y]` is the KV secret key identifier.

> NOTE: Prefix `V_` is also included into KV secret identifier. 

## Todo

- [ ] Add the ability to create multiple KV engines. 

## Author

Image wrapper created by Miłosz Gilga.
<br>
Original Vault image created by [Hashicorp Vault](https://github.com/hashicorp/vault/blob/main/Dockerfile).
<br>
If you have any questions about this software send message: [personal@miloszgilga.pl](mailto:personal@miloszgilga.pl).

## License
This software is on GNU-GPL 3.0 license.