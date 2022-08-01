# Mattermost-preview  & Monk

This repository contains Monk.io template to deploy Mattermost-preview system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Nginx as a reverse proxy to work with  Mattermost-preview out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk Mattermost-preview repository

In order to load templates and change configuration simply use below commands: 
```bash
git clone https://github.com/kaganmersin/monk-mattermost.git

# and change directory to the mattermost-preview template folder
cd mattermost-preview
```


## Configuration

You can add/remove configuration of the template.

The current variables can be found in `mattermost-preview/stack/variables` section

```yaml
variables:
    nginx-listen-port:
      type: int
      value: 8081
    mattermost-server-name:
      type: string
      value: "mm.example.com"
    mattermost-image-tag: "latest"
    nginx-image-tag: "latest"
```

##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **nginx-listen-port** | Configures the ports that the nginx listens on. | int | 8081 |
| **mattermost-server-name** | Configure the fqdn that nginx will accept and route to. | string | mm.example.com |
| **mattermost-image-tag** | Mattermost-preview image version. | string | latest |
| **nginx-image-tag** | Nginx image version. | string | latest |



## Local Deployment

First clone the repository and change the current directory to the /mattermost-preview folder and simply run below command after launching `monkd`:
:

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 mattermost-preview/nginx
 │  └─🧩 mattermost-preview/mattermost
 └─🔗 Process groups:
    └─🧩 mattermost-preview/stack
✔ All templates loaded successfully

➜  monk list mattermost-preview

✔ Got the list
Type      Template                         Repository          Version      Tags
runnable  mattermost-preview/mattermost    local               -            -
runnable  mattermost-preview/nginx         local               -            -
group     mattermost-preview/stack         local               -            -
runnable  nginx/latest                     mattermost-preview  -            -
runnable  nginx/reverse-proxy              mattermost-preview  -            -
runnable  nginx/reverse-proxy-ssl-certbot  mattermost-preview  1.15-alpine  nginx, reverse proxy, ssl, certbot


➜  monk run mattermost-preview/stack

✔ Started mattermost-preview/stack
```

This will start the entire mattermost-preview/stack stack with a Nginx reverse proxy. 

To access mattermost-preview from local system, required  dns entry needs to be added in local host file as following format: 

```
 127.0.0.1 <mattermost-server-name>
  ```

## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name mattermost-preview
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add -p gcp -f <path/to/your-key.json>
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Cloud provider gcp
? Name of the new instance my-instance
? Tags (split by whitespace) mattermost
? Region europe-central2
? Zone europe-central2-a
? Instance type e2-medium
? Number of instances (or press ENTER to use default = 1) 3
? Default disk type for gcp is HDD (pd-standard). Would you like to change it? No
? Disk size (or press ENTER to use default = 250 GBs) 50
✔ Start creation of new instance(s) on gcp... DONE
✔ Creating node: my-instance-1 DONE
✔ Initializing node: my-instance-1 DONE
✔ Creating node: my-instance-2 DONE
✔ Initializing node: my-instance-2 DONE
✔ Creating node: my-instance-3 DONE
✔ Initializing node: my-instance-3 DONE
✔ Connecting: my-instance-1 DONE
✔ Syncing peer: my-instance-1 DONE
✔ Connecting: my-instance-2 DONE
✔ Connecting: my-instance-3 DONE
✔ Syncing peer: my-instance-2 DONE
✔ Syncing peer: my-instance-3 DONE
✔ Syncing nodes DONE
✔ Cluster grown successfully
```

Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 mattermost-preview/nginx
 │  └─🧩 mattermost-preview/mattermost
 └─🔗 Process groups:
    └─🧩 mattermost-preview/stack
✔ All templates loaded successfully

➜  monk list mattermost-preview

✔ Got the list
Type      Template                         Repository          Version      Tags
runnable  mattermost-preview/mattermost    local               -            -
runnable  mattermost-preview/nginx         local               -            -
group     mattermost-preview/stack         local               -            -
runnable  nginx/latest                     mattermost-preview  -            -
runnable  nginx/reverse-proxy              mattermost-preview  -            -
runnable  nginx/reverse-proxy-ssl-certbot  mattermost-preview  1.15-alpine  nginx, reverse proxy, ssl, certbot

➜  monk run mattermost-preview/stack

✔ Started mattermost-preview/stack
```
## Logs & Shell

```bash
# show Mattermost-preview logs
➜  monk logs -l 1000 -f mattermost-preview/mattermost

# show Nginx logs
➜  monk logs -l 1000 -f mattermost-preview/nginx

# access shell in the container running Mattermost
➜  monk shell mattermost-preview/mattermost

# access shell in the container running Nginx
➜  monk shell mattermost-preview/nginx
```

## Stop, remove and clean up workloads and templates

```bash
➜ monk purge -x mattermost-preview/stack mattermost-preview/mattermost mattermost-preview/nginx

✔ mattermost-preview/stack purged
✔ mattermost-preview/mattermost purged
✔ mattermost-preview/nginx purged
```