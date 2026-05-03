
# Configuration Update on Main-Device

Notes:

* The tedge-agent (running on the) listens to a local MQTT Broker. It listens to everything under topic `te/device/main/#`

* The tedge-mapper sends the command to `te/device/main///cmd/#`

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
box darkblue Device <br>host=rpi5-2ccf674408e9.local
participant Tedge-mapper
participant Http-proxy as C8Y-Http-Proxy
participant Tedge-agent
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Send Configuration"
Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=524,<br>rpi5-2ccf674408e9,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/2414333104,<br>tedge-configuration-plugin
Tedge-mapper->>Tedge-mapper: Convert <br>server-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/2414333104 to<br>remote-url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/2414333104
Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/main///cmd/config_update/c8y-mapper-14334078
Note right of Tedge-agent: log to file <br>/var/log/tedge/agent<br>/workflow-config_update-c8y-mapper-14334078.log<br><br>-log progress to mqtt topic<br>te/device/main///cmd/config_update/c8y-mapper-14334078
Tedge-agent->>Tedge-agent: init cfg_update
Tedge-agent->>Tedge-agent: executing cfg_update

Tedge-agent->>+Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/2414333104
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>-Tedge-agent: 

Tedge-agent->>Tedge-agent: store file as<br>/tmp/config_update_c8y-mapper-14334078
Tedge-agent->>Tedge-agent: prepare cfg_update
Tedge-agent->>Tedge-agent: set cfg_update
Tedge-agent->>Tedge-agent: verify cfg_update
Tedge-agent-->>-Tedge-mapper: Report success to topic<br>te/device/main///cmd/config_update/c8y-mapper-14334078
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us<br>Message=506,14334078
Cumulocity->>User: Report success
```


# Configuration update on Child-Device

Configuration Updates on a Child-Device are very similar than on the parent-/main Device. The difference in this scenario:

* the tedge-agent (running on child-device) listens to an MQTT Broker hosted on the Main Device. It listens to everything under `te/device/{child-id}/#`

* the tedge-mapper (running on main-device) sends the command to `te/device/{child-id}/#`

> All communication between Tedge-mapper/Tedge-agent & between Tedge-mapper/Cumulocity goes via an MQTT Broker hosted on the Device. This was abstracted for simplicity reasons.

Diagram:

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
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Send Configuration on child-device"

Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=524,<br>rpi4-a3f1b8d2e9c4,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/1214336041,<br>tedge-configuration-plugin

Tedge-mapper->>Tedge-mapper: Convert <br>server-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/1214336041 to<br>remote-url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/1214336041

Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/config_update/c8y-mapper-14338320

Note right of Tedge-agent: log to file<br>/var/log/tedge/agent<br>/workflow-config_update-c8y-mapper-14338320.log<br><br>-log progress to mqtt topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/config_update/c8y-mapper-14338320
Tedge-agent->>Tedge-agent: init cfg_update
Tedge-agent->>Tedge-agent: executing cfg_update

Tedge-agent->>+Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/1214336041
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>-Tedge-agent: 

Tedge-agent->>Tedge-agent: store file as<br>/tmp/config_update_c8y-mapper-14338320
Tedge-agent->>Tedge-agent: prepare cfg_update
Tedge-agent->>Tedge-agent: set cfg_update
Tedge-agent->>Tedge-agent: verify cfg_update
Tedge-agent-->>-Tedge-mapper: Report success to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/config_update/c8y-mapper-14338320
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us/rpi4-a3f1b8d2e9c4<br>Message=506,14338320
Cumulocity->>User: Report success
```