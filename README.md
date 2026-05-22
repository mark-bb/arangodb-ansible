# ArangoDB [un]installing

Cluster configuration only.  
With ssl support.  

## Notes
- The only playbok at the moment is `arangodb.yml` 
- dbservers & coordinators are in the `[dbservers]` group
- agents are in the `[agents]` group
- Distributions & certs must be placed to the `roles/arangodb/files/` directory. Its current contents is:
```
$ tree roles/arangodb/files/
roles/arangodb/files/
├── certs
│   ├── truststore.pem
│   ├── fqdn-01.pem
│   ├── fqdn-02.pem
│   └── fqdn-03.pem
└── distrib
    └── arangodb3_3.11.9-1_amd64.deb -> /distrib/arango/arangodb3_3.11.9-1_amd64.deb
```
- Certificates generation  
  - Node cert & key file in the PEM format must have the `<inventory_hostname>.pem` name  
  - `truststore.pem` may contain a single root CA cert or root CA & intermediate CA certs

## Commands examples
- Installing
```
ansible-playbook arangodb.yml -e "adb_config_only=no"
```
- Reconfiguring
```
ansible-playbook arangodb.yml
```
- Reconfiguring ssl <-> non-ssl
```
ansible-playbook arangodb.yml -e "adb_ssl_reconfig=yes"
```
- Stopping
```
ansible-playbook arangodb.yml -e "play_action=stop"
```
- Starting
```
ansible-playbook arangodb.yml -e "play_action=start"
```
- Online upgrade  
  - Ensure that the cluster is running
  - Copy / create symlink to the desired arangodb package in the `roles/arangodb/files/distrib` directory
  - Remove an old package / link from this directory
```
ansible-playbook arangodb.yml -e "play_action=upgrade-online"
```
An example of debug output of an upgrade call:
```
ok: [VTB-Scylla-RHEL-04.vm.stsoft.lan] => {
    "res": {
        "changed": true,
        "cmd": "sudo -u arangodb arangodb upgrade --starter.endpoint=http://localhost:8528",
        "delta": "0:01:21.260002",
        "end": "2024-08-07 06:19:57.990376",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2024-08-07 06:18:36.730374",
        "stderr": "\u001b[90m2024-08-07T06:18:36-04:00\u001b[0m |INFO| Database automatic upgrade from version 3.11.8 to version 3.11.9 has been started \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:18:36-04:00\u001b[0m |INFO| Servers upgraded: none, remaining servers: 3 agents, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:18:44-04:00\u001b[0m |INFO| Servers upgraded: 1 agent, remaining servers: 2 agents, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:18:57-04:00\u001b[0m |INFO| Servers upgraded: 2 agents, remaining servers: 1 agent, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:19:19-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, remaining servers: 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:19:28-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 1 dbserver, remaining servers: 2 coordinators, 1 dbserver \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:19:38-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 2 dbservers, remaining servers: 2 coordinators \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:19:48-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 1 coordinator, 2 dbservers, remaining servers: 1 coordinator \u001b[36mcomponent=\u001b[0marangodb\n\u001b[90m2024-08-07T06:19:57-04:00\u001b[0m |INFO| Database upgrade has finished \u001b[36mcomponent=\u001b[0marangodb",
        "stderr_lines": [
            "\u001b[90m2024-08-07T06:18:36-04:00\u001b[0m |INFO| Database automatic upgrade from version 3.11.8 to version 3.11.9 has been started \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:18:36-04:00\u001b[0m |INFO| Servers upgraded: none, remaining servers: 3 agents, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:18:44-04:00\u001b[0m |INFO| Servers upgraded: 1 agent, remaining servers: 2 agents, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:18:57-04:00\u001b[0m |INFO| Servers upgraded: 2 agents, remaining servers: 1 agent, 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:19:19-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, remaining servers: 2 coordinators, 2 dbservers \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:19:28-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 1 dbserver, remaining servers: 2 coordinators, 1 dbserver \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:19:38-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 2 dbservers, remaining servers: 2 coordinators \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:19:48-04:00\u001b[0m |INFO| Servers upgraded: 3 agents, 1 coordinator, 2 dbservers, remaining servers: 1 coordinator \u001b[36mcomponent=\u001b[0marangodb",
            "\u001b[90m2024-08-07T06:19:57-04:00\u001b[0m |INFO| Database upgrade has finished \u001b[36mcomponent=\u001b[0marangodb"
        ],
        "stdout": "",
        "stdout_lines": []
    }
}
```
- Uninstalling
```
ansible-playbook arangodb.yml -e "play_action=uninstall"
```

## Useful commands  
- Status  

with username & password:
```
curl -s --user "root:" -X GET http://<coordinator-host>:8529/_admin/status | jq -r
```
or with jwt token:
```
t=$(curl -s -X POST \
  'http://<coordinator-host>:8529/_open/auth' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{"password": "", "username": "root"}' | jq -r '.jwt')

curl -s -X GET \
  'http://<coordinator-host>:8529/_admin/status' \
  -H "Authorization: bearer ${t?}" \
| jq
```
- Agency config

on an agent host using the `arangodb` utility:
```
curl -s -X GET -H "$(sudo arangodb auth header --auth.jwt-secret=/etc/arangodb/arangodb.secret)" \
http://localhost:8531/_api/agency/config \
| jq -r
```
or on a client host using the `jwtgen` utility having access to the secret file locally:
```
hdr="Authorization: bearer $(jwtgen -s $(cat files/secret/arangodb.secret) -e 3600 -a "HS256" -c 'iss=arangodb' -c 'server_id=myclient')"

curl -s -X GET -H "${hdr?}" \
http://<agent-host>:8531/_api/agency/config \
| jq -r
```
