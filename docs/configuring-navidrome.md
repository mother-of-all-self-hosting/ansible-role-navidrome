<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Navidrome

This is an [Ansible](https://www.ansible.com/) role which installs [Navidrome](https://www.navidrome.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Navidrome is a [Subsonic-API](http://www.subsonic.org/pages/api.jsp) compatible music server.

See the project's [documentation](https://www.navidrome.org/docs/) to learn what Navidrome does and why it might be useful to you.

## Adjusting the playbook configuration

To enable Navidrome with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# navidrome                                                            #
#                                                                      #
########################################################################

navidrome_enabled: true

########################################################################
#                                                                      #
# /navidrome                                                           #
#                                                                      #
########################################################################
```

### Set the hostname

To enable Navidrome you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
navidrome_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Disabling scanner (optional)

Navidrome is configured to enable automatic scanning of the music library. You can disable it by adding the following configuration to your `vars.yml` file:

```yaml
navidrome_environment_variable_nd_scanner_enabled: false
```

### Configuring scan schedule (optional)

By default Navidrome scans the library each minute. You can adjust the schedule by adding the following configuration to your `vars.yml` file:

```yaml
navidrome_environment_variable_nd_scanner_schedule: SCHEDULE_VALUE_HERE
```

The Golang cron syntax is accepted. Refer to [this page](https://pkg.go.dev/github.com/robfig/cron) for details.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `navidrome_environment_variables_additional_variables` variable

Refer to [the official documentation](https://www.navidrome.org/docs/usage/configuration/options/#environment-variables) for a complete list of Navidrome's config options that you can put in `navidrome_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Navidrome becomes available at the specified hostname like `https://example.com`.

To get started, open the URL with a web browser to create an administrator account. You can create additional users (admin-privileged or not) after that.

You can also connect various Subsonic-API-compatible [apps](https://www.navidrome.org/docs/overview/#apps) (desktop, web, mobile) to your Navidrome instance.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu navidrome` (or how you/your playbook named the service, e.g. `mash-navidrome`).

#### Increase logging verbosity

If you want to increase the verbosity, add the following configuration to your `vars.yml` file:

```yaml
# Valid values are: error, warn, info, debug, trace
navidrome_environment_variable_nd_loglevel: debug
```
