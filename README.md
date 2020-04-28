# Ansible Role: haproxy

Installs HAProxy

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    haproxy_socket: /var/lib/haproxy/stats

The socket through which HAProxy can communicate.

    haproxy_chroot: /var/lib/haproxy

The jail directory where chroot() will be performed before dropping privileges.

    haproxy_user: haproxy
    haproxy_group: haproxy

Default user and group under which HAProxy should run.

    haproxy_selinux_cmd: haproxy_connect_any
    haproxy_selinux_enable: true

When selinux in enabled, it is nessasary to allow haproxy to connect for all TCP Ports. You can disable this, set `haproxy_selinux_enable: false`

    haproxy_default_vars: 
        - 'log          global'
        - 'maxconn      8000'
        - 'mode         http'
        - 'option       httplog'
        - 'option       dontlognull'
        - 'option       http-server-close'
        - 'option       forwardfor       except 127.0.0.0/8'
        - 'option       redispatch'
        - 'retries      3'
        - 'timeout      http-request 60s'
        - 'timeout      queue 1m'
        - 'timeout      connect 10s'
        - 'timeout      client 1m'
        - 'timeout      server 1m'
        - 'timeout      http-keep-alive 10s'
        - 'timeout      check 10s'
        - 'no option    http-keep-alive'

Default Vars for HAProxy.

    haproxy_global_vars:
    - 'ssl-default-bind-ciphers ABCD+KLMJ:...'
    - 'ssl-default-bind-options no-sslv3'

A list of extra global variables to add to the global configuration section inside `haproxy.cfg.`

    define_cookies: true

Cookies option can be add

    haproxy_tls_certificates:
        - filename: oer-poc.pem
          key: !vault |
              $ANSIBLE_VAULT;1.1;AES256
              37306361356466326163353
              ....
              36346564333065376533
          cert: |
                -----BEGIN CERTIFICATE-----
                MIIDNzCCAh+gAwIBAgIUQa8
                ....
                YkSjXk3vKF0/lG8=
                -----END CERTIFICATE-----

Create authentication cert an key. This file will be saved in the `haproxy_ssl_folder: /etc/haproxy/ssl/`
    haproxy_ca_certificates:      
        - filename: test2.crt
          cert: |
                -----BEGIN CERTIFICATE-----
                MIIDNzCCAh+gAwIBAgIUQa8MayU8q7fGS4q/+LthuEbcBfkwDQYJKoZIhvcNAQEL
                .....
                CWuYkBFiOHxsmX7pQdxtPGOF1Xh0t/G1zkaIzrzaZM1BDc7VEGx4M0LWMTL+qCTU
                YkSjXk3vKF0/lG8=
                -----END CERTIFICATE-----

Creates ca authentication file

## Dependencies

None.

## Example Playbook

    - hosts: appserver
      roles:
        - haproxy

## Example listen pools

    - poolname: nextcloud
          listen_ports: 21443
          backend_ports: 80
          bind_options: ['ssl', 'crt' ,'/tmp/test.pem']
          mode: http
          server_names: 
            - server_name: oer-poc-app01
              ipadress: 172.29.65.130
            - server_name: oer-poc-app02
              ipadress: 172.29.65.131
          member_options: ['cookie', 'check']
          cookies: ['SRVNAME', 'insert', 'indirect', 'nocache']
          listener_options:
          options: ['httplog', 'forwardfor', 'log-health-checks']
          balance: roundrobin



## License

Apache-2.0

## Author Information

This role was created in 2020 by Gregor Bartels