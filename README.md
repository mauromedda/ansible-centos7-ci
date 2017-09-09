# CentOS 7+ Ansible CI image


This project contains the Dockerfile to build a docker image useful to test Ansible playooks.

# How to build the image

The latest version of the image will be pushed on Docker Hub automatically everytime that a commit is made or merged to the `master` branch. If you want just build the image on your own box, execute the following steps:

  1. [Install docker engine](https://docs.docker.com/engine/installation/)
  2. Clone the repo and `cd` on this directory
  3. Run `docker build -t ansible-centos7-ci -f Dockerfile .`

# How to use the image

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. Pull this image from Docker Hub: `docker pull mauromedda/ansible-centos7-ci:latest` (or use the tag you built earlier, e.g. `ansible-centos7-ci`).
  3. Run a container from the image: `docker run --detach --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro mauromedda/ansible-centos7-ci:latest /usr/lib/systemd/systemd` (to test my Ansible roles, I add in a volume mounted from the current working directory with ``--volume=`pwd`:/etc/ansible/roles/role_under_test:ro``).
  4. Use Ansible inside the container:
    a. `docker exec --tty [container_id] env TERM=xterm ansible --version`
    b. `docker exec --tty [container_id] env TERM=xterm ansible-playbook /path/to/ansible/playbook.yml --syntax-check`

## Notes

I use Docker to test my Ansible roles and playbooks on CentOS 7 using CI tools, usually Jenkins and Travis. This container allows me to test roles and playbooks using Ansible running locally inside the container.

> **Important Note**: I use this image for testing in an isolated environment—not for production—and the settings and configuration used may not be suitable for a secure and performant production environment. Use on production servers/in the wild at your own risk!

## Author

Created in 2017 by Mauro Medda. Inspired by the work of [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
