# Standalone Image Transfer for oVirt

## Motivation

Remove usage of CDI for disk copying, as CDI's imageio client is less efficient

## How

Use ovirt-img from ovirt-imageio in a standalone pod. 

Command flags:
```shell
 ovirt-img download-disk [-h] [-c CONFIG] [--engine-url ENGINE_URL] [--username USERNAME] [--password-file PASSWORD_FILE] [--cafile CAFILE] [--log-file LOG_FILE]
                               [--log-level LOG_LEVEL] [-f {raw,qcow2}] [--max-workers MAX_WORKERS] [--buffer-size BUFFER_SIZE]
                               disk_id filename
```

An Init Container can setup the config file before startup:
```
[engine]

# oVirt engine API URL (required).
engine_url = https://fqdn

# oVirt engine API username (required).
username = admin@ovirt@internalsso

# oVirt enigne API password. If not specified the example script will get the
# password from stdin (optional).
password = pass

# Verify server certificate and host name (optional, default yes).
secure = no

cafile = /home/ca.pem
```

It's probably possible to set it up with:
```yaml
          command:
            - /bin/sh
            - -c
            - |
              #!/bin/sh
              mkdir ~/.config/
              echo $OVIRT_CA > ~/ca.pem
              cat << EOF > ~/.config/ovirt-img.conf
              [engine]
              engine_url = $OVIRT_URL
              username = $OVIRT_USERNAME
              password = $OVIRT_PASSWORD
              secure = no
              cafile = /root/ca.pem
              EOF
```
