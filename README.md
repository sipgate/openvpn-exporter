# OpenVPN Exporter

[![license](https://img.shields.io/github/license/natrontech/openvpn-exporter)](https://github.com/natrontech/openvpn-exporter/blob/main/LICENSE)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/natrontech/openvpn-exporter/badge)](https://securityscorecards.dev/viewer/?uri=github.com/natrontech/openvpn-exporter)
[![release](https://img.shields.io/github/v/release/natrontech/openvpn-exporter)](https://github.com/natrontech/openvpn-exporter/releases)
[![go-version](https://img.shields.io/github/go-mod/go-version/natrontech/openvpn-exporter)](https://github.com/natrontech/openvpn-exporter/blob/main/go.mod)
[![Go Report Card](https://goreportcard.com/badge/github.com/natrontech/openvpn-exporter)](https://goreportcard.com/report/github.com/natrontech/openvpn-exporter)
[![SLSA 3](https://slsa.dev/images/gh-badge-level3.svg)](https://slsa.dev)

---

Export [OpenVPN Community](https://openvpn.net/community-downloads/) statistics to [Prometheus](https://prometheus.io/).

Metrics are retrieved using the OpenVPN Status file.

## Credits

This project is based on the [OpenVPN Exporter](https://github.com/kumina/openvpn_exporter) by [Kumina](https://www.kumina.nl/).

## Exported Metrics

| Metric                                           | Meaning                                                                | Labels                                                                                        |
| ------------------------------------------------ | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| openvpn_up                                       | Whether scraping OpenVPN's metrics was successful.                     | `status_path`                                                                                 |
| openvpn_status_update_time_seconds               | UNIX timestamp at which the OpenVPN statistics were updated.           | `status_path`                                                                                 |
| openvpn_server_connected_clients                 | Number Of Connected Clients                                            | `status_path`                                                                                 |
| openvpn_server_client_received_bytes_total       | Amount of data received over a connection on the VPN server, in bytes. | `status_path`, `common_name`, `connection_time` `real_address`, `virtual_address`, `username` |
| openvpn_server_client_sent_bytes_total           | Amount of data sent over a connection on the VPN server, in bytes.     | `status_path`, `common_name`, `connection_time` `real_address`, `virtual_address`, `username` |
| openvpn_server_route_last_reference_time_seconds | Time at which a route was last referenced, in seconds.                 | `status_path`, `common_name`, `real_address` `virtual_address`                                |
| openvpn_client_tun_tap_read_bytes_total          | Total amount of TUN/TAP traffic read, in bytes.                        | `status_path`                                                                                 |
| openvpn_client_tun_tap_write_bytes_total         | Total amount of TUN/TAP traffic written, in bytes.                     | `status_path`                                                                                 |
| openvpn_client_tcp_udp_read_bytes_total          | Total amount of TCP/UDP traffic read, in bytes.                        | `status_path`                                                                                 |
| openvpn_client_tcp_udp_write_bytes_total         | Total amount of TCP/UDP traffic written, in bytes.                     | `status_path`                                                                                 |
| openvpn_client_auth_read_bytes_total             | Total amount of authentication traffic read, in bytes.                 | `status_path`                                                                                 |
| openvpn_client_pre_compress_bytes_total          | Total amount of data before compression, in bytes.                     | `status_path`                                                                                 |
| openvpn_client_post_compress_bytes_total         | Total amount of data after compression, in bytes.                      | `status_path`                                                                                 |
| openvpn_client_pre_decompress_bytes_total        | Total amount of data before decompression, in bytes.                   | `status_path`                                                                                 |
| openvpn_client_post_decompress_bytes_total       | Total amount of data after decompression, in bytes.                    | `status_path`                                                                                 |

> **Note:** When `openvpn.ignore-individuals` is set to `true`, all server metrics will be aggregated per `status_path`:
> - `openvpn_server_client_received_bytes_total` and `openvpn_server_client_sent_bytes_total`: Summed across all clients
> - `openvpn_server_route_last_reference_time_seconds`: Most recent (maximum) timestamp across all routes
>
> Individual client data is not distinguishable anymore. This is important when OpenVPN is configured with `username-as-common-name`, as the `common_name` would otherwise contain user information.

## Flags / Environment Variables

```bash
$ ./openvpn-exporter -help
```

You can use the following flags to configure the exporter. All flags can also be set using environment variables. Environment variables take precedence over flags.

| Flag                         | Environment Variable         | Description                                                                                     | Default                       |
| ---------------------------- | ---------------------------- | ----------------------------------------------------------------------------------------------- | ----------------------------- |
| `openvpn.loglevel`           | `OPENVPN_LOGLEVEL`           | Log level (debug, info)                                                                         | `info`                        |
| `openvpn.status-files`       | `OPENVPN_STATUS_FILES`       | Path to OpenVPN status file. Can be a comma separated list                                      | `/var/log/openvpn-status.log` |
| `openvpn.metrics-path`       | `OPENVPN_METRICS_PATH`       | Path under which to expose metrics                                                              | `/metrics`                    |
| `openvpn.listen-address`     | `OPENVPN_LISTEN_ADDRESS`     | Address to listen on for web interface and telemetry                                            | `:9176`                       |
| `openvpn.ignore-individuals` | `OPENVPN_IGNORE_INDIVIDUALS` | Aggregate all client metrics instead of exporting individual client data. Server metrics will only use `status_path` as label. Individual labels (`common_name`, `connection_time`, `real_address`, `virtual_address`, `username`) are not exported. | `false`                       |

## Release

Each release of the application includes Go-binary archives, checksums file, SBOMs and container images. 

The release workflow creates provenance for its builds using the [SLSA standard](https://slsa.dev), which conforms to the [Level 3 specification](https://slsa.dev/spec/v1.0/levels#build-l3). Each artifact can be verified using the `slsa-verifier` or `cosign` tool (see [Release verification](SECURITY.md#release-verification)).
