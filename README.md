# Timeout by the SRCF

Stack used:
* Ansible for configuration management on servers
* BBB for the core video-conferencing software
* Greenlight for session management frontend
* Scalelite for load balancing
* Prometheus and Grafana (possibly) for monitoring

Useful resource: the repo with privacy stuff

Installation instructions below taken right from stadtulm:

### Prepare

```
git submodule update --init
ansible-galaxy install -r requirements.yml
```

Note: `cloudalchemy.node-exporter` requires the gnu variant of `tar` on macOS. (`brew install gnu-tar`)  
Note: `cloudalchemy.prometheus` requires the `jmespath` python module on your (deployer) machine


You need to create the file `vault_password` and put the ansible vault password in there.

### Run
```
ansible-playbook main.yml
```

## HowTo
### Add new machine
* Update DNS zonefile in `files/coredns/zones/`
 * add A and AAAA records
 * update serial (`yyyymmddvv` with `vv` being the version increment. E.g., 2020040101)
* Update DNS by `ansible-playbook main.yml --tags dns`
* Enter Hostname twice in `inventory`, below `[all]` and below the other role the machine should have, eg. `[bbb]`
* Confirm that you can ssh into the machine by its newly aquired dns name (this also adds the host key to your `~/.ssh/known_hosts`)
* run `ansible-playbook main.yml -l your.fresh.hostname.example` (you may need `--user root` if you don't have an user yet, the base role creates one for you)
* ...?
* register your new bbb instance:
  * at the monitoring by running `ansible-playbook main.yml --tags monitoring`
  * at the loadbalancer by running `ansible-playbook main.yml --tags config`
* enable it manually in the loadbalancer
