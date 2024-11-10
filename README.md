# openvpn-ansible

Ansible playbook to set up an OpenVPN server and automatically generate .ovpn client configuration files.

This Ansible setup configures an OpenVPN server and generates client configuration files (.ovpn) automatically. All certificates (client, server, and ta.key) are stored in the `/etc/openvpn/server/` directory. By default, client configuration files (including the default config file and the .ovpn file) are stored in the `/home/ubuntu/` directory. To change this location, see the `generate-client` role.

## Configuration in `group_vars`

To customize settings such as the private network range, client name, remote server IP, or routing options, update the `main.yml` file located in `group_vars`.

```yml
openvpn_port: 1194
openvpn_protocol: udp
openvpn_server_ip: "10.8.0.0"   # Private network of the OpenVPN server
openvpn_netmask: "255.255.255.0"
openvpn_dev: "tun0"             # Name of the interface device

# Route settings
route_ip: "route 10.20.30.0 255.255.255.0"

# Paths
openvpn_cert_path: "/etc/openvpn/server"
easy_rsa_path: "/etc/openvpn/easy-rsa"
openvpn_easyrsa_path: "/usr/share/easy-rsa"

openvpn_client_server: "REMOTE-SERVER-IP" # remote server the clients would connect

## name of client/new-client
client_name: "client"
```

## Firewall Rules

A `MASQUERADE` rule is set to allow connections to all private networks using the OpenVPN configuration. To make this rule persistent, uncomment the following line in the firewall role:

```yml
- name: Save iptables rules
  command: iptables-save > /etc/iptables/rules.v4
```

## Generating Client Configurations

By default, the playbook applies all roles and generates a client configuration file (`name-of-client.ovpn`) in the `/home/ubuntu/` directory.

To create a new client:

- Update the `client_name` variable in `group_vars`.
- Comment out all roles in the playbook except `generate-client`.
- Run the Ansible playbook to generate the new client configuration.
