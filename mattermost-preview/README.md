# Mattermost-preview  & Monk

This repository contains Monk.io template to deploy Mattermost-preview system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Nginx as a reverse proxy a Mattermost-preview out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk Mattermost-preview repository

In order to load templates and change configuration simply use below command: 
```bash
git clone https://github.com/kaganmersin/monk-mattermost.git
```


## Configuration

You can add/remove configuration of the template.

The current variables can be found in `mattermost-preview/stack/variables` section

```yaml
variables:
    nginx-listen-port:
      type: int
      value: 8080
    mattermost-server-name:
      type: string
      value: "monk-mattermost.com"
    mattermost-image-tag: "latest"
    nginx-image-tag: "latest"
```

##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **nginx-listen-port** | Configures the ports that the nginx listens on. | int | 8080 |
| **mattermost-server-name** | Configure the fqdn that nginx will accept and route to. | string | monk-mattermost.com |
| **mattermost-image-tag** | Mattermost-preview image version. | string | latest |
| **nginx-image-tag** | Nginx image version. | string | latest |



## Local Deployment

This template is available directly from Monkhub.io therefore if you want a quick deploy simply run below command after launching `monkd`:

```bash
➜  monk load MANIFEST
✔ Got the list
Type      Template                       Repository  Version  Tags
runnable  mattermost-preview/mattermost  local       -        -
runnable  mattermost-preview/nginx       local       -        -
group     mattermost-preview/stack       local       -        -

➜  monk run mattermost-preview/stack

✔ Started mattermost-preview/stack
```

This will start the entire mattermost-preview/stack stack with a Nginx reverse proxy. 

## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name directus-deployment
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add
? Cloud provider gcp
? GOOGLE_APPLICATION_CREDENTIALS is set. Try load it? Yes
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Name for the new instance my-instance
? Tags (split by whitespace) directus
? Instance region (gcp) europe-west2
? Instance zone (gcp) europe-west2-c
? Instance type (gcp) e2-standard-2
? Disk Size (GBs) 60
? Number of instances (or press ENTER to use default = 1) 2
✔ Creating a new instance(s) on gcp...
✔ Syncing nodes DONE
✔ Cluster grown successfully
```

Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).

```bash
➜  monk load MANIFEST
✔ Got the list
Type      Template                       Repository  Version  Tags
runnable  mattermost-preview/mattermost  local       -        -
runnable  mattermost-preview/nginx       local       -        -
group     mattermost-preview/stack       local       -        -

➜  monk run mattermost-preview/stack

✔ Started mattermost-preview/stack
```

### Logs & Shell

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

