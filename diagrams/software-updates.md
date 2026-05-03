
# Artifact in Cumulocity Software Repository

## Software Update on Main-Device

Device Setup:

* The tedge-agent (running on the) listens to a local MQTT Broker. It listens to everything under topic `te/device/main/#`

* The tedge-mapper sends the command to `te/device/main///cmd/#`

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
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Install Software Package"
Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=528,<br>rpi5-2ccf674408e9,<br>tedge-environment-migration,1.0.0,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/6212620356,<br>install
Tedge-mapper->>Tedge-mapper: Convert<br>server-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/6212620356 to <br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/6212620356
Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/main///cmd/software_update/c8y-mapper-14346775
Note right of Tedge-agent: log to file<br>/var/log/tedge/agent<br>/workflow-software_update-c8y-mapper-14346775.log<br><br>-log progress to mqtt topic<br>te/device/main///cmd/software_update/c8y-mapper-14346775
Tedge-agent->>Tedge-agent: Find proper sw-mgmt-plugin<br>(based on softwaretype)
Tedge-agent->>Tedge-agent: init software_update
Tedge-agent->>Tedge-agent: scheduled software_update
Tedge-agent->>Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/6212620356
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>Tedge-agent: 
Tedge-agent->>Tedge-agent: store file to<br>/tmp/tedge-environment-migration_1.0.0
Tedge-agent->>Tedge-agent: executing software_update
Tedge-agent->>Tedge-agent: delete file /tmp/tedge-environment-migration_1.0.0
Tedge-agent-->>-Tedge-mapper: Report success to topic<br>te/device/main///cmd/software_update/c8y-mapper-14346775
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us<br>Message=506,14346775
Cumulocity->>User: Report success
Tedge-agent->>Tedge-agent: Query installed software
Tedge-agent->>Tedge-mapper: Report list of currently installed software via topic<br>te/device/main///cmd/software_list/c8y-mapper-{timestamp}
Tedge-mapper->>Cumulocity: Report list of currently installed software via topic<br>c8y/s/us
Cumulocity->>User: Report list of currently installed software
```

## Software update on Child-Device

Software Updates on a Child-Device are very similar than on the parent-/main Device. The difference in this scenario:

* the tedge-agent (running on child-device) listens to an MQTT Broker hosted on the Main Device. It listens to everything under `te/device/{child-id}/#`

* the tedge-mapper (running on main-device) sends the command to `te/device/{child-id}/#`

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
box Main-Device <br>host=rpi5-2ccf674408e9.local
participant Tedge-mapper
participant Http-proxy as C8Y-Http-Proxy
end
box Child-Device (host=rpi4-a3f1b8d2e9c4)
participant Tedge-agent
end
Note over Http-proxy: Hosted by tedge-mapper service

User->>Cumulocity: Click "Install Software Package"
Cumulocity->>Tedge-mapper: Topic=c8y/s/ds<br>Message=528,<br>rpi4-a3f1b8d2e9c4,<br>tedge-environment-migration,1.0.0,<br>https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/6212620356,<br>install
Tedge-mapper->>Tedge-mapper: Convert<br>server-url=https://t2021486094.eu-latest.cumulocity.com/inventory/binaries/6212620356 to <br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/6212620356
Tedge-mapper->>+Tedge-agent: Send converted Message to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/software_update/c8y-mapper-14347027
Note right of Tedge-agent: log to file<br>/var/log/tedge/agent<br>/workflow-software_update-c8y-mapper-14347027.log<br><br>-log progress to mqtt topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/software_update/c8y-mapper-14347027
Tedge-agent->>Tedge-agent: Find proper sw-mgmt-plugin<br>(based on softwaretype)
Tedge-agent->>Tedge-agent: init software_update
Tedge-agent->>Tedge-agent: scheduled software_update
Tedge-agent->>Http-proxy: download file from<br>url=http://rpi5-2ccf674408e9.local:8001/c8y/inventory/binaries/6212620356
Http-proxy->>Cumulocity: download file
Cumulocity-->>Http-proxy: 
Http-proxy-->>Tedge-agent: 
Tedge-agent->>Tedge-agent: store file to<br>/tmp/tedge-environment-migration_1.0.0
Tedge-agent->>Tedge-agent: executing software_update
Tedge-agent->>Tedge-agent: delete file /tmp/tedge-environment-migration_1.0.0
Tedge-agent-->>-Tedge-mapper: Report success to topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/software_update/c8y-mapper-14347027
Tedge-mapper->>Cumulocity: Report success<br>Topic=c8y/s/us/rpi4-a3f1b8d2e9c4<br>Message=506,14347027
Cumulocity->>User: Report success
Tedge-agent->>Tedge-agent: Query installed software
Tedge-agent->>Tedge-mapper: Report list of currently installed software via topic<br>te/device/rpi4-a3f1b8d2e9c4///cmd/software_list/c8y-mapper-{timestamp}
Tedge-mapper->>Cumulocity: Report list of currently installed software via topic<br>c8y/s/us/rpi4-a3f1b8d2e9c4
Cumulocity->>User: Report list of currently installed software
```



