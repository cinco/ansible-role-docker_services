# Docker services catalogue
This role contains a collection of services that run on a Docker Swarm, each service is segmented in it's own (set of) network(s). And all persistent storage is bind-mounted into the container, this allows for placing that data on shared storage, such as NFS or GlusterFS.

The catalogus currently contains:

* Drone: a CI/CD controller with agents (https://github.com/drone/drone)
* Gitea: Go powered Git server with webGUI (https://github.com/go-gitea/gitea)
* Portainer: Docker management inferface (https://github.com/portainer/portainer)
* AWX: Ansible Tower's upstream (https://github.com/ansible/awx)
* Traefik: Docker integrated reverse proxy (https://github.com/containous/traefik)
* Docker registry: local docker image storage
* Janitor: a docker that will periodically run 'docker system prune -f' to clean up all unused stuff

This role has a few mandatory parts that will be provisioned which (most, if not all) other services depend upon:

* Portainer
* Janitor
* Traefik

# Deployment
In order to use the services from this catalogue, do the following:

* Install docker (tip: use Jeff Geerlings' Docker role)
* Create the {{ docker_data_dir }}, preferably on shared storage
* Copy ``` defaults/main.yml ``` and configure it
* Run the role with a playbook such as:

```
---
- hosts: 'docker'
  roles:
    - 'docker_services'
```

# Reaching services
After provisioning all services will be reachable under the following name:

```
https://{{ service_app_name }}.{{ traefik_domain }}
```

Which translates to something like https://portainer.dckr.example.com

# Extra actions
Some services require some more actions before they work, below is are the instructions per service if they require additional setup.

## Wiki.js
Do this before running the playbook (or you will get some errors)
* Download https://github.com/Requarks/wiki-v1/blob/master/config.sample.yml and fill it in. Save it as {{ docker_data_dir }}/config.yml
* Generate a SSH key and place that in {{ docker_data_dir }}/wikijs_data/ssh
* Create a new Git repo in your Git server and make sure it has some contents
* Run the playbook

After running the playbook, you should be able to login using {{ wikijs_init_admin_email }} / admin123
