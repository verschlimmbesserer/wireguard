Wireguard
=========

This role installs `wireguard` and generates the nessesary keys as the configurations as well.
It uses the  `ansible.builtin.slurp` collection to pickup the generated private and public keys. If the files exists or after the got generated. It is also possible to use a pre generated Private Key in the interface dictionary under the wireguard variable.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

|name|type|function|
|---|---|---|
|`wireguard_public_key_prefix:`| str | prefix for the wireguard public key|
| `wireguard_key_filename:`| str | name used for the key filenames, when not set will default to `{{ ansible_hostname }}`|
|`wireguard_create_keys:`| bool | By default Ansible will attempt to creaet the wireguard private and public key. |
|`wireguard`| dict | contains the configuration for the `wg0.conf` with the keys `interface` and `peers` which contains each `peer` as dictionary.|

Example Variables Configuration
------------

Site 2 Site VPN, when both Host are managed by Ansible in the same group.
Host in the US:

```Yaml
wireguard_private_key_name: "{{ ansible_hostname }}"
wireguard_create_keys: true

wireguard:
  interface:
    ListenPort: 51820
    Address: 10.10.9.2/32
    PostUp: iptables -A FORWARD -i wg0 -j ACCEPT
    PostDown: iptables -D FORWARD -i wg0 -j ACCEPT
  peers:
    wg01-euro:
      PersistentKeepalive: 25
      Endpoint: "{{ hostvars['wg01-euro']['ansible_default_ipv4.address'] }}:51820"
      PublicKey: "{{ hostvars['wg01-euro']['wg_public_key']}}"
      AllowedIPs: 10.10.0.0/24,10.10.9.0/30
```

Host in Europe:
```Yaml
wireguard_private_key_name: "{{ ansible_hostname }}"
wireguard_create_keys: true

wireguard:
  interface:
    ListenPort: 51820
    Address: 10.10.9.1/32
    PostUp: iptables -A FORWARD -i wg0 -j ACCEPT
    PostDown: iptables -D FORWARD -i wg0 -j ACCEPT
  peers:
    wg01-usa:
      PersistentKeepalive: 25
      Endpoint: "{{ hostvars['wg01-usa']['ansible_default_ipv4.address'] }}:51820"
      PublicKey: "{{ hostvars['wg01-usa']['wg_public_key']}}"
      AllowedIPs: 10.10.0.0/24,10.10.9.0/30
```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

GNU General Public License v3.0

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
