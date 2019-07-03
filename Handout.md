### Ansible
Konfigurationssprache für Server, Infrastruktur/SDN 

* Module
https://docs.ansible.com/ansible/2.4/list_of_all_modules.html 
* Installation
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-node

* Playbook

```Ansible
- hosts: <host_name> # see hosts file
  remote_user: root

   roles:
     - role: inoxio.proxmox_vms
       proxmox:
         api_user: <encrypted user>
         api_password: <encrypted password>
         api_host: <encrypted host>
       vms:
         <vm1_name>:
           node: <node_name>
           ubuntu_distribution: <distribution_name>
           locale: en_US
           root_password:  <encrypted root password>
           memory_size: <ram_size_in_MB>
           virtio: '{"virtio0":"local-lvm:<disk_size_in_GB>,cache=writeback,discard=on"}'
           network:
             ip: <ip>
             netmask: <netmask>
             gateway: <gateway_ip>
             nameserver: <nameserver1> <nameserver2>
             domainname: <domainname>
           additional_packages:
              - curl
              - gnupg
           scripts:
             - files/scripts/my_script.sh

         <vm2_name>:
           node: <node_name>
           root_password: <encrypted root password>
           ubuntu_distribution: 'xenial'
           memory_size: <ram_size_in_MB>
           virtio: '{"virtio0":"local-lvm:<disk_size_in_GB>,cache=writeback,discard=on"}'
           network:
             ip: <ip>
             netmask: <netmask>
             gateway: <gateway_ip>
             nameserver: <nameserver1> <nameserver2>
             domainname: <domainname>
```
Tasks
```Ànsible
---
- name: install docker through pip
  pip:
    name:
      - docker
      - docker-compose

- name: Pull nextcloud image
  docker_image:
    name: nextcloud:{{ nextcloud_image }}
    force: yes

- name: Pull redis image
  docker_image:
    name:  webhippie/redis:latest
    force: yes

- name: docker-compose via ansible docker_service
  docker_service:
    project_name: "nextcloud-services"
    definition:
      version: '3'
      services:
        nextcloud:
          image: nextcloud:{{ nextcloud_image }}
          ports:
            - 1080:80
          environment:
            - MYSQL_DATABASE={{ nextcloud.domain }}
            - MYSQL_USER={{ nextcloud.db_username }}
            - MYSQL_PASSWORD={{ nextcloud.db_password }}
            - MYSQL_HOST={{ nextcloud.db_host }}
            - NEXTCLOUD_ADMIN_USER={{ nextcloud.admin_username }}
            - NEXTCLOUD_ADMIN_PASSWORD={{ nextcloud.admin_password }}
          volumes:
            - /mnt/data/nextcloud-data:/var/www/html/data
            - /mnt/data/nextcloud-data/config:/var/www/html/config
          restart: always

        redis:
          image: webhippie/redis:latest
          restart: always
          environment:
            - REDIS_DATABASES=1
          healthcheck:
            test: ["CMD", "/usr/bin/healthcheck"]
            interval: 30s
            timeout: 10s
            retries: 5
    state: present
    restarted: true

```


----------------------------------------------------------------------
#### Icinga
#### Proxmox
#### ISPConfig
### AWS
#### Terraform
