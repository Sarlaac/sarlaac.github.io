---
title: Portainer Deployment options
date: 2023-01-16 2:11 # HH:MM format
categories: howto
tags: portainer docker # always lowercase
---

# Portainer Deployment Options

Two different approaches on how to deploy Portainer, and Docker containers within Portainer.

## Option 1: Ansible
I'm a big fan of ansible, and try to build all my infrastructure, configuration and maintenance tasks with it. You can read about Ansible on 
the [documentation](https://docs.ansible.com/ansible/latest/index.html) site. Or watch a great series on youtube at [Jeff Greeling](https://www.jeffgeerling.com/blog/2020/ansible-101-jeff-geerling-youtube-streaming-series).

I created a repository that lets someone with no ansible experience bootstrap the installation, and then run a playbook (automated tasks) 
to install Docker, Portainer, and a Minecraft container. It's a fun way to get started with Ansible and Portainer.
- [Ansible Portainer Deployment](https://github.com/Sarlaac/ansible_mc_server)


## Option 2: Github referenced Portainer compose files
This option is fairly simple, in that you are just storing your docker-compose files on github, and pointing Portainer to the repository.
You can tell Portainer to watch the repository for changes, and restart containers as the compose files change, or you can leave it manual, and restart
containers only if you want the new configuration taken.
- [Portainer Github repository](https://github.com/Sarlaac/portainer_plays)
