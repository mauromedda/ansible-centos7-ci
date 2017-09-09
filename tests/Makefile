# This is a Makefile useful to wrap Ansible CI test in Jenkins or Travis Pipelines
# It executes four tests:
#   - syntax check
#   - linting check
#   - execution
#   - idempotence

all: dpull dprep syn lint run idemp clean

.PHONY: all

DOCKER_OPTS := --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro
DOCKER_INIT := /usr/lib/systemd/systemd
DOCKER_IMAGE ?= mauromedda/ansible-centos7-ci:latest

ifeq ($(CONTAINER_ID),)
  CONTAINER_ID := $(shell date +%s)
endif

dpull:
	docker pull $(DOCKER_IMAGE)

dprep:
	docker run --detach $(DOCKER_OPTS) --name $(CONTAINER_ID) --volume=$(PWD):/etc/ansible/roles/role_under_test:rw $(DOCKER_IMAGE) $(DOCKER_INIT)
	if [[ -f requirements.yml ]]; then \
	  docker exec --tty $(CONTAINER_ID) env TERM=xterm ansible-galaxy install -r /etc/ansible/roles/role_under_test/requirements.yml ;\
	fi

syn:
	docker exec --tty $(CONTAINER_ID) env TERM=xterm ansible-playbook /etc/ansible/roles/role_under_test/tests/test.yml --syntax-check

lint:
	docker exec --tty $(CONTAINER_ID) env TERM=xterm ansible-lint /etc/ansible/roles/role_under_test/tests/test.yml

run:
	docker exec --tty $(CONTAINER_ID) env TERM=xterm env ANSIBLE_FORCE_COLOR=1 ansible-playbook /etc/ansible/roles/role_under_test/tests/test.yml

idemp:
	docker exec --tty $(CONTAINER_ID) env TERM=xterm env ANSIBLE_FORCE_COLOR=1 ansible-playbook /etc/ansible/roles/role_under_test/tests/test.yml ;\
	| grep -q 'changed=0.*failed=0' ;\
    	&& (printf 'Idempotence test: pass'"\n") ;\
	|| (printf 'Idempotence test: fail'"\n" && exit 1)
	
	
clean:
	docker stop $(CONTAINER_ID)
	docker rm $(CONTAINER_ID)
