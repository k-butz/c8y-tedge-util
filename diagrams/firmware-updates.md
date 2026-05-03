# Firmware Update on Main-Device

Note:

* Firmware Updates are highly individual and utilize the [User-defined operations workflows](https://thin-edge.github.io/thin-edge.io/references/agent/operation-workflow/). Below example is based on the Rugix firmware workflow in [meta-tedge](https://github.com/thin-edge/meta-tedge). 

* Below shows a successful firmware update. In case of a failed firmware-update, Rugix-Ctrl would do a rollback to previous Device Partition. Have a look at [this](https://github.com/thin-edge/meta-tedge/blob/kirkstone/meta-tedge-rugix/recipes-tedge/tedge-firmware-rugix/tedge-firmware-rugix/rugix_workflow.sh) file for process details. 

> All communication between Tedge-mapper/Tedge-agent & between Tedge-mapper/Cumulocity goes via an MQTT Broker hosted on the Device. This was abstracted for simplicity reasons.

```mermaid
sequenceDiagram
autonumber
box User
actor User
end
box Cumulocity
participant Cumulocity
end
box Device <br>host=rpi5-2ccf674408e9.local
participant Tedge-mapper 
participant Http-proxy as C8Y-Http-Proxy
participant Tedge-agent
participant Rugix-ctrl
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Install Firmware Update"
Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=515,<br>rpi5-2ccf674408e9,<br>tedge-rugix-image-tryboot-arm64,20251204.1247,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/7514349933
Tedge-mapper->>Tedge-mapper: Convert<br>remote-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/7514349933 to <br>tedge-url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/7514349933
Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/main///cmd/firmware_update/c8y-mapper-14351253
Note right of Tedge-agent: log to file <br>/var/log/tedge/agent<br>/workflow-firmware_update-c8y-mapper-14351253.log<br><br>-log progress to mqtt topic<br>te/device/main///cmd/firmware_update/c8y-mapper-14351253
Tedge-agent->>Tedge-agent: Find workflow in<br>/etc/tedge/operations/firmware_update.toml
Tedge-agent->>Tedge-agent: init
Tedge-agent->>Tedge-agent: scheduled
Tedge-agent->>Tedge-agent: execute<br>(check if current partition is the active partition)

Tedge-agent->>+Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/7514349933
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>-Tedge-agent: 

Tedge-agent->>Rugix-ctrl: instruct to install image
Rugix-ctrl->>Rugix-ctrl: install image
Rugix-ctrl-->>Tedge-agent: 
Tedge-agent->>Tedge-agent: restart<br>in new partition
Tedge-agent->>Tedge-agent: verify device-health

Tedge-agent->>Rugix-ctrl: instruct to commit image
Rugix-ctrl->>Rugix-ctrl: commit image
Rugix-ctrl-->>Tedge-agent: 

Tedge-agent->>Tedge-mapper: Report current firmware to topic<br>te/device/main///twin/firmware
Tedge-mapper->>Cumulocity: Update firmware information in cloud via topic <br>c8y/inventory/managedObjects/update/rpi5-2ccf674408e9

Tedge-agent->>-Tedge-mapper: Report success to topic<br>te/device/main///cmd/firmware_update/c8y-mapper-14351253
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us<br>Message=506,14351253
Cumulocity->>User: Report success
```


# Firmware Update on Child-Device

Firmware Updates on a Child-Device are very similar than on the parent-/main Device. The difference in this scenario:

* the tedge-agent (running on child-device) listens to an MQTT Broker hosted on the Main Device. It listens to everything under `te/device/{child-id}/#`

* the tedge-mapper (running on main-device) sends the command to `te/device/{child-id}/#`

Further Notes:

* Firmware Updates are highly individual and utilize the [User-defined operations workflows](https://thin-edge.github.io/thin-edge.io/references/agent/operation-workflow/). Below example is based on the Rugix firmware workflow in [meta-tedge](https://github.com/thin-edge/meta-tedge). 

* Below shows a successful firmware update. In case of a failed firmware-update, Rugix-Ctrl would do a rollback to previous Device Partition. Have a look at [this](https://github.com/thin-edge/meta-tedge/blob/kirkstone/meta-tedge-rugix/recipes-tedge/tedge-firmware-rugix/tedge-firmware-rugix/rugix_workflow.sh) file for process details. 


> All communication between Tedge-mapper/Tedge-agent & between Tedge-mapper/Cumulocity goes via an MQTT Broker hosted on the Device. This was abstracted for simplicity reasons.

```mermaid
sequenceDiagram
autonumber
box darkred User
actor User
end
box darkgreen Cumulocity
participant Cumulocity
end
box darkblue Main-Device <br>host=rpi5-2ccf674408e9.local
participant Tedge-mapper
participant Http-proxy as C8Y-Http-Proxy
end
box purple Child-Device <br>host=rpi4-a3f1b8d2e9c4
participant Tedge-agent
participant Rugix-ctrl
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Install Firmware Update"
Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=515,<br>rpi4-a3f1b8d2e9c4,<br>tedge-rugix-image-tryboot-arm64,20251204.1247,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/7514349933
Tedge-mapper->>Tedge-mapper: Convert<br>remote-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/7514349933 to <br>tedge-url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/7514349933
Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/firmware_update/c8y-mapper-15462364
Note right of Tedge-agent: - log to file<br>/var/log/tedge/agent<br>/workflow-firmware_update-c8y-mapper-15462364.log<br><br>-log progress to mqtt topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/firmware_update/c8y-mapper-15462364
Tedge-agent->>Tedge-agent: Find workflow in<br>/etc/tedge/operations/firmware_update.toml
Tedge-agent->>Tedge-agent: init
Tedge-agent->>Tedge-agent: scheduled
Tedge-agent->>Tedge-agent: execute<br>(check if current partition is the active partition)

Tedge-agent->>+Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/7514349933
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>-Tedge-agent: 

Tedge-agent->>Rugix-ctrl: instruct to install image
Rugix-ctrl->>Rugix-ctrl: install image
Rugix-ctrl-->>Tedge-agent: 
Tedge-agent->>Tedge-agent: restart<br>in new partition
Tedge-agent->>Tedge-agent: verify device-health

Tedge-agent->>Rugix-ctrl: instruct to commit image
Rugix-ctrl->>Rugix-ctrl: commit image
Rugix-ctrl-->>Tedge-agent: 

Tedge-agent->>Tedge-mapper: Report current firmware to topic<br>te/device/rpi4-a3f1b8d2e9c4///twin/firmware
Tedge-mapper->>Cumulocity: Update firmware information in cloud via topic <br>c8y/inventory/managedObjects/update/rpi4-a3f1b8d2e9c4
Tedge-agent->>-Tedge-mapper: Report success to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/firmware_update/c8y-mapper-15462364
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us/rpi4-a3f1b8d2e9c4<br>Message=506,15462364
Cumulocity->>User: Report success
```