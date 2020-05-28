# Timeout Videoconferencing by the SRCF

[Documentation](https://docs.srcf.net/timeout)

[Videoconferencing](https://timeout.srcf.net)

Stack used:
* Ansible for configuration management on servers
* BBB for the core video-conferencing software
* Greenlight for session management frontend
* Scalelite for load balancing
* Prometheus and Grafana (possibly) for monitoring

## Installation

### Pre-run

```
git submodule update --init
ansible-galaxy install -r requirements.yml
```

Note: `cloudalchemy.node-exporter` requires the gnu variant of `tar` on macOS. (`brew install gnu-tar`)  
Note: `cloudalchemy.prometheus` requires the `jmespath` python module on your (deployer) machine

You need to create the file `vault_password` (if you know, you know)

### Run
```
ansible-playbook -u <your user> -t <the tag> main.yml
```
Use the relevant tags:
* offloader for changes to latency
* greenlight for changes the greenlight
* loadbalancer for changes to scalelite
* register-bbbs to register a new BBB instance (make sure to flush the redis cache before)
* bbb-install for a new BBB machine
* base must be run before any of these
* base-monitoring for adding node exporter

### Adding a new machine
* Ensure that DNS is properly configured
* Enter Hostname twice in `inventory`, below `[all]` and below the other role the machine should have, eg. `[bbb]`
* Make sure your user can SSH into said machine
* run `ansible-playbook main.yml -t install-bbb`
* register your new bbb instance:
  * at the monitoring by running `ansible-playbook main.yml --tags base-monitoring`
  * at the loadbalancer by running `ansible-playbook main.yml --tags register-bbbs`
* enable it manually in the loadbalancer
