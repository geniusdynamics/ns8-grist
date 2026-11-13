# ns8-grist

Relational spreadsheet that combines the flexibility of a spreadsheet with the robustness of a database, for self-hosted data management.

Module id: `grist`. Images are pinned in `build-images.sh` and updated through reviewed changes.

## Install

```bash
add-module ghcr.io/geniusdynamics/grist:latest 1
```

The command returns the instance id, for example `grist1`. Use the id NS8 returns.

## Configure

```bash
api-cli run configure-module --agent module/grist1 --data - <<'EOF'
{
  "host": "grist.example.org",
  "http2https": true,
  "lets_encrypt": false,
  "default_email": ""
}
EOF
```

| Parameter | Required | Type | Description |
|---|---|---|---|
| `host` | yes | string | Host name for the application, like 'grist.example.org' |
| `lets_encrypt` | yes | boolean | Request a valid Let's Encrypt certificate. |
| `http2https` | yes | boolean | Redirect all the HTTP requests to HTTPS |
| `default_email` | yes | string | Email address of the Grist installation administrator and owner of the first team site |
| `single_org` | no | string | Name of the single team site served at the root of the host, lowercase letters, digits and hyphens. Empty to serve multiple team sites under /o/<name> |

## Configuration output

`get-configuration` returns: `host`, `lets_encrypt`, `http2https`, `default_email`, `single_org`.

## Images

| Image | Version |
|---|---|
| `docker.io/gristlabs/grist` | `1.7.22` |

## Network and access

- Reserves 1 TCP port(s) on the node. The web application is published through Traefik on the configured host name.
- Runs rootless.
- Declares these NS8 authorizations: `traefik@node:routeadm`.

## Backup

The following paths are included in the NS8 backup (`imageroot/etc/state-include.conf`):

- `state/passwords.env`
- `volumes/grist-data`

## Services

- `grist.service`

## Get the configuration

```bash
api-cli run get-configuration --agent module/grist1
```

## Update and uninstall

```bash
api-cli run update-module --data '{"module_url":"ghcr.io/geniusdynamics/grist:latest","instances":["grist1"],"force":true}'
remove-module --no-preserve grist1
```

## Testing

Continuous integration:

- `Publish images` builds the module image on every push.
- `Test module` installs the image on a QEMU NS8 node and runs the Robot Framework suite in `tests/`. It runs after `Publish images` succeeds on `dev` and on `main`.
- `Build API JSON Schema docs` and the security scan run alongside.

The suite installs the module, configures a host name, checks that the web application answers through Traefik, takes UI screenshots and removes the module.

Run it locally with the NS8 standard infrastructure described in the [ns8-github-actions README](https://github.com/NethServer/ns8-github-actions/blob/v1/README.md#running-tests-locally).

## Upstream

Application documentation: https://support.getgrist.com/self-managed/

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
