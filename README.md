# Ansible Mautrix Telegram

Matrix Mautrix Telegram bridge using Docker.

Runs and configures a Mautrix Telegram instance.

Traefik friendly via Docker container labels. This role is rather opinionated 
though more configurability is welcome via pull requests.

This repository is also mirrored on GitHub at https://github.com/jaywink/ansible-mautrix-telegram

## Installing

`ansible-galaxy install jaywink.ansible_mautrix_telegram`

## Configuration

### Required

```yaml
mautrix_telegram_homeserver_name: domain.tld
mautrix_telegram_homeserver_domain: matrix.domain.tld
mautrix_telegram_appservice_base_url: https://mautrix-telegram.domain.tld
mautrix_telegram_appservice_database: sqlite:///filename.db or postgres://username:password@hostname/dbname
mautrix_telegram_appservice_as_token: changemetosomethingsecret
mautrix_telegram_appservice_hs_token: changemetosomethingsecret
mautrix_telegram_bridge_permissions:
  "*": "relaybot"
  "public.example.com": "user"
  "example.com": "full"
  "@admin:example.com": "admin"
mautrix_telegram_telegram_api_id: getfromtelegram
mautrix_telegram_telegram_api_hash: getfromtelegram
```

### Optional

These defaults will be used if no values provided.

```yaml
mautrix_telegram_docker_image: dock.mau.dev/mautrix/telegram:v0.14.0
# Use for example to hook up with Traefik
# Ensure to use `PathPrefixStrip:/mautrix-telegram` in your frontend
# rule for Traefik, should you keep the default path mount. Mautrix-Telegram
# will not recognize the requests unless the path is stripped.
mautrix_telegram_docker_labels: []
# Docker network to attach to
mautrix_telegram_docker_network: default
# Routing
mautrix_telegram_appservice_path: "/mautrix-telegram"
mautrix_telegram_appservice_public_path: "{{ mautrix_telegram_appservice_path }}/public"
mautrix_telegram_telegram_bot_token: disabled
# Max size of remote groups/channels to bridge (-1 for any size)
mautrix_telegram_bridge_max_member_count: "-1"
# Sync at startup
mautrix_telegram_bridge_startup_sync: true
```

### Appservice

You'll also need to template in the following into your Matrix homeserver appservice 
registration file.

```yaml
id: mautrix-telegram
as_token: "{{  mautrix_telegram_appservice_as_token }}"
hs_token: "{{ mautrix_telegram_appservice_hs_token }}"
namespaces:
  users:
  - exclusive: true
    regex: '@telegram_.+:{{ mautrix_telegram_homeserver_name }}'
  aliases:
  - exclusive: true
    regex: '#telegram_.+:{{ mautrix_telegram_homeserver_name }}'
url: "{{ mautrix_telegram_appservice_base_url }}{{ mautrix_telegram_appservice_path }}"
sender_localpart: telegrambot
rate_limited: false
de.sorunome.msc2409.push_ephemeral: true
push_ephemeral: true
```

## License

Apache 2.0
