# Setup

Setup for my thin-edge.io installation with:

* tedge-agent, tedge-mapper-c8y and mosquitto installed on my RPI5

* tedge-agent installsed on my RPI4

...with RPI4 being a child-device of RPI5

**RPI5 configuration (main-device)**

Below the complete configuration list. For usage with a child-device keep special attention to: `c8y.proxy.client.host`, `mqtt.client.host` and `http.client.host`. These need to be the hostname of your Device, not `127.0.0.1`. 

```
$ tedge config list
device.id=rpi5-2ccf674408e9
device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
device.csr_path=/etc/tedge/device-certs/tedge.csr
device.type=tedge-raspios-arm64-tryboot
device.cryptoki.mode=off
device.cryptoki.pin=123456
device.cryptoki.socket_path=/run/tedge-p11-server/tedge-p11-server.sock
certificate.organization=Thin Edge
certificate.organization_unit=Device
certificate.validity.requested_duration=365d
certificate.validity.minimum_duration=30d
c8y.url=iot-demo.eu-latest.cumulocity.com
c8y.root_cert_path=/etc/ssl/certs
c8y.auth_method=certificate
c8y.credentials_path=/etc/tedge/credentials.toml
c8y.http=iot-demo.eu-latest.cumulocity.com:443
c8y.mqtt=iot-demo.eu-latest.cumulocity.com:8883
c8y.topics=["te/+/+/+/+", "te/+/+/+/+/twin/+", "te/+/+/+/+/m/+", "te/+/+/+/+/m/+/meta", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
c8y.device.id=rpi5-2ccf674408e9
c8y.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
c8y.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
c8y.device.csr_path=/etc/tedge/device-certs/tedge.csr
c8y.smartrest.templates=[]
c8y.smartrest.use_operation_id=true
c8y.smartrest.child_device.create_with_device_marker=false
c8y.smartrest1.templates=[]
c8y.enable.log_upload=true
c8y.enable.config_snapshot=true
c8y.enable.config_update=true
c8y.enable.firmware_update=true
c8y.enable.device_profile=true
c8y.enable.device_restart=true
c8y.enable.software_update=true
c8y.mapper.mqtt.max_payload_size=16184
c8y.proxy.bind.address=127.0.0.1
c8y.proxy.bind.port=8001
c8y.proxy.client.host=rpi5-2ccf674408e9.local
c8y.proxy.client.port=8001
c8y.bridge.topic_prefix=c8y
c8y.bridge.keepalive_interval=60s
c8y.bridge.include.local_cleansession=auto
c8y.entity_store.auto_register=true
c8y.entity_store.clean_start=true
c8y.software_management.api=legacy
c8y.software_management.with_types=false
c8y.operations.auto_log_upload=on-failure
c8y.availability.enable=true
c8y.availability.interval=60m
c8y.mqtt_service.enabled=false
c8y.mqtt_service.topics=[]
az.root_cert_path=/etc/ssl/certs
az.topics=["te/+/+/+/+/m/+", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
az.device.id=rpi5-2ccf674408e9
az.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
az.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
az.device.csr_path=/etc/tedge/device-certs/tedge.csr
az.mapper.timestamp=true
az.mapper.timestamp_format=unix
az.mapper.mqtt.max_payload_size=262144
az.bridge.topic_prefix=az
az.bridge.keepalive_interval=60s
aws.root_cert_path=/etc/ssl/certs
aws.topics=["te/+/+/+/+/m/+", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
aws.device.id=rpi5-2ccf674408e9
aws.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
aws.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
aws.device.csr_path=/etc/tedge/device-certs/tedge.csr
aws.mapper.timestamp=true
aws.mapper.timestamp_format=unix
aws.mapper.mqtt.max_payload_size=131072
aws.bridge.topic_prefix=aws
aws.bridge.keepalive_interval=60s
mqtt.topic_root=te
mqtt.device_topic_id=device/main//
mqtt.bind.enabled=false
mqtt.bind.address=127.0.0.1
mqtt.bind.port=1883
mqtt.client.host=rpi5-2ccf674408e9.local
mqtt.client.port=1883
mqtt.bridge.built_in=true
mqtt.bridge.reconnect_policy.initial_interval=5s
mqtt.bridge.reconnect_policy.maximum_interval=10m
mqtt.bridge.reconnect_policy.reset_window=5m
http.bind.port=8000
http.bind.address=127.0.0.1
http.client.port=8000
http.client.host=rpi5-2ccf674408e9.local
agent.state.path=/data/tedge/agent
agent.enable.config_update=true
agent.enable.config_snapshot=true
agent.enable.log_upload=true
agent.entity_store.auto_register=true
agent.entity_store.clean_start=true
software.plugin.default=apt
software.plugin.max_packages=1000
software.plugin.exclude=^(glibc|lib|kernel-|iptables-module).*
run.path=/run
run.lock_files=true
run.log_memory_interval=0
logs.path=/var/log/tedge
tmp.path=/tmp
data.path=/var/tedge
firmware.child.update.timeout=1h
service.type=service
service.timestamp_format=unix
apt.dpkg.options.config=keepold
sudo.enable=true
diag.plugin_paths=["/usr/share/tedge/diag-plugins"]
log.plugin_paths=["/usr/share/tedge/log-plugins"]
configuration.plugin_paths=["/usr/share/tedge/config-plugins"]
flows.memory.heap_size=16777216
flows.memory.stack_size=262144
flows.stats.interval=1h
flows.stats.on_message=false
flows.stats.on_interval=false
flows.stats.on_startup=false
flows.params.keep_on_delete=false
```


**RPI4 configuration (child-device)**

Below the complete configuration list for the child-device. Keep special attention to: `c8y.proxy.client.host`, `mqtt.client.host` and `http.client.host`. These need to be the hostname of your Main-/Parent-Device, not `127.0.0.1`. 

```
$ tedge config list
device.id=rpi4-a3f1b8d2e9c4
device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
device.csr_path=/etc/tedge/device-certs/tedge.csr
device.type=thin-edge.io
device.cryptoki.mode=off
device.cryptoki.pin=123456
device.cryptoki.socket_path=/run/tedge-p11-server/tedge-p11-server.sock
certificate.organization=Thin Edge
certificate.organization_unit=Device
certificate.validity.requested_duration=365d
certificate.validity.minimum_duration=30d
c8y.root_cert_path=/etc/ssl/certs
c8y.auth_method=certificate
c8y.credentials_path=/etc/tedge/credentials.toml
c8y.topics=["te/+/+/+/+", "te/+/+/+/+/twin/+", "te/+/+/+/+/m/+", "te/+/+/+/+/m/+/meta", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
c8y.device.id=rpi4-a3f1b8d2e9c4
c8y.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
c8y.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
c8y.device.csr_path=/etc/tedge/device-certs/tedge.csr
c8y.smartrest.templates=[]
c8y.smartrest.use_operation_id=true
c8y.smartrest.child_device.create_with_device_marker=false
c8y.smartrest1.templates=[]
c8y.enable.log_upload=true
c8y.enable.config_snapshot=true
c8y.enable.config_update=true
c8y.enable.firmware_update=true
c8y.enable.device_profile=true
c8y.enable.device_restart=true
c8y.enable.software_update=true
c8y.mapper.mqtt.max_payload_size=16184
c8y.proxy.bind.address=127.0.0.1
c8y.proxy.bind.port=8001
c8y.proxy.client.host=rpi5-2ccf674408e9.local
c8y.proxy.client.port=8001
c8y.bridge.topic_prefix=c8y
c8y.bridge.keepalive_interval=60s
c8y.bridge.include.local_cleansession=auto
c8y.entity_store.auto_register=true
c8y.entity_store.clean_start=true
c8y.software_management.api=legacy
c8y.software_management.with_types=false
c8y.operations.auto_log_upload=on-failure
c8y.availability.enable=true
c8y.availability.interval=60m
c8y.mqtt_service.enabled=false
c8y.mqtt_service.topics=[]
az.root_cert_path=/etc/ssl/certs
az.topics=["te/+/+/+/+/m/+", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
az.device.id=rpi4-a3f1b8d2e9c4
az.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
az.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
az.device.csr_path=/etc/tedge/device-certs/tedge.csr
az.mapper.timestamp=true
az.mapper.timestamp_format=unix
az.mapper.mqtt.max_payload_size=262144
az.bridge.topic_prefix=az
az.bridge.keepalive_interval=60s
aws.root_cert_path=/etc/ssl/certs
aws.topics=["te/+/+/+/+/m/+", "te/+/+/+/+/e/+", "te/+/+/+/+/a/+", "te/+/+/+/+/status/health"]
aws.device.id=rpi4-a3f1b8d2e9c4
aws.device.key_path=/etc/tedge/device-certs/tedge-private-key.pem
aws.device.cert_path=/etc/tedge/device-certs/tedge-certificate.pem
aws.device.csr_path=/etc/tedge/device-certs/tedge.csr
aws.mapper.timestamp=true
aws.mapper.timestamp_format=unix
aws.mapper.mqtt.max_payload_size=131072
aws.bridge.topic_prefix=aws
aws.bridge.keepalive_interval=60s
mqtt.topic_root=te
mqtt.device_topic_id=device/rpi4-a3f1b8d2e9c4//
mqtt.bind.enabled=true
mqtt.bind.address=127.0.0.1
mqtt.bind.port=1883
mqtt.client.host=rpi5-2ccf674408e9.local
mqtt.client.port=1883
mqtt.bridge.built_in=true
mqtt.bridge.reconnect_policy.initial_interval=5s
mqtt.bridge.reconnect_policy.maximum_interval=10m
mqtt.bridge.reconnect_policy.reset_window=5m
http.bind.port=8000
http.bind.address=127.0.0.1
http.client.port=8000
http.client.host=rpi5-2ccf674408e9.local
agent.state.path=/data/tedge/agent
agent.enable.config_update=true
agent.enable.config_snapshot=true
agent.enable.log_upload=true
agent.entity_store.auto_register=true
agent.entity_store.clean_start=true
software.plugin.default=apt
software.plugin.max_packages=1000
run.path=/run
run.lock_files=true
run.log_memory_interval=0
logs.path=/var/log/tedge
tmp.path=/tmp
data.path=/var/tedge
firmware.child.update.timeout=1h
service.type=service
service.timestamp_format=unix
apt.dpkg.options.config=keepold
sudo.enable=true
diag.plugin_paths=["/usr/share/tedge/diag-plugins"]
log.plugin_paths=["/usr/share/tedge/log-plugins"]
configuration.plugin_paths=["/usr/share/tedge/config-plugins"]
flows.memory.heap_size=16777216
flows.memory.stack_size=262144
flows.stats.interval=1h
flows.stats.on_message=false
flows.stats.on_interval=false
flows.stats.on_startup=false
flows.params.keep_on_delete=false
```

# Troubleshooting for child-device connectivity

In case of issues (especially with Operations) on your child-device, make sure below netcat commands succeed on your child-device:

```sh
nc -zv rpi5-2ccf674408e9.local 1883
nc -zv rpi5-2ccf674408e9.local 8000
nc -zv rpi5-2ccf674408e9.local 8001
```

If these fail or time-out:

* Check the hostname of your remote-device is correct (and can be resolved)

* Check if the firewall rules on your main device reject them. Use `sudo nft list ruleset` on your main-device to double-check.

