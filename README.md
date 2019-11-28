GitLab Runner [![Build Status](https://api.travis-ci.org/eRadical/ansible-gitlab-runner.svg?branch=master)](https://travis-ci.org/eRadical/ansible-gitlab-runner) [![Ansible Role](https://img.shields.io/badge/role-eRadical.ansible--gitlab--runner-blue.svg?maxAge=2592000)](https://galaxy.ansible.com/eRadical/ansible-gitlab-runner/)
=============

This role will install the [official GitLab Runner](https://gitlab.com/gitlab-org/gitlab-runner)
(fork from [DBLaci](https://github.com/DBLaci/ansible-gitlab-runner), which forked it from [riemers](https://github.com/riemers/ansible-gitlab-runner), which in turn forked it from [haroldb](https://github.com/haroldb/ansible-gitlab-runner)) with updates. Needed something simple and working, this did the trick for me. Open for changes though.

# NOTE:
Even though it's a fork it is *NOT* backward compatible:
- it will only install gitlab-runner (all "gitlab-multi-runner" references were scraped)
- all per-runner global variables were removed
- there is no "default runner"
- there is a list of runner to be configured (1 machine can have more than 1 runner... *be bold*)


Requirements
------------

This role requires Ansible 2.0 or higher.

Role Variables
--------------

`gitlab_runner_concurrent`
The maximum number of jobs to run concurrently.
Defaults to the number of processor cores.

`gitlab_check_interval`
The interval of job poll in seconds
Defaults to the upstream default (3 seconds)

`gitlab_runner_list`
This list will hold the multiple runners. It is a list of dict based on the settings available in one runner. You must set different descriptions for the runners to work.

# NOTE:
Runners are created for the first time. You can't change the settings of existsing ones with this role.

See the [config for more options](https://github.com/eRadical/ansible-gitlab-runner/blob/master/tasks/register-runner.yml)

Example Playbook
----------------
```yaml
- hosts: runner_hosts
  become: yes
  roles:
    - eRadical.gitlab-runner
```

Example for requirements.yml
```yaml
- name: eRadical.gitlab-runner
  src: https://github.com/eRadical/ansible-gitlab-runner
  scm: git
  version: commit_id_here
```

Inside `group_vars/runner_hosts.yml`
```yaml
gitlab_runner_concurrent: 1
gitlab_check_interval: 3
gitlab_runner_list:
- coordinator_url: 'https://your.gitlab.installation/ci'
  registration_token: ''
  description: '{{ ansible_hostname }}__shell'
  output_limit: 102400	# 100MB - --output-limit flag is in Kb
  executor: 'shell'
  tags:
  - shell
  - bash
- coordinator_url: 'https://your.gitlab.installation/ci'
  registration_token: ''
  description: '{{ ansible_hostname }}__docker'
  output_limit: 102400	# 100MB - --output-limit flag is in Kb
  executor: 'docker'
  docker_image: "centos:latest"
  docker_tlsverify: "false"
  docker_pull_policy: "always"
  docker_privileged: "true"
  tags:
  - docker
  - container
```
