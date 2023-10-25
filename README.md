# gve_devnet_appdynamics_ansible_agent_upgrade
This repo contains example Ansible scripts to automatically update AppDynamic Agents. 


## Contacts
* Kevin Chen (kevchen3@cisco.com)

## Solution Components
* Python
* Ansible


## Installation/Configuration

Follow the official Ansible documentation for the most up-to-date installation procedure
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#control-node-requirements

Quick instructions for installing Ansible on a Linux based or MacOS system with Python 3.9 or newer

```
python3 -m pip install --user ansible

```

This repository is based on the official AppDynamics documentation here:
https://docs.appdynamics.com/appd/23.x/latest/en/application-monitoring/install-app-server-agents/agent-management/standalone-host-platforms/ansible 


## Add your controller settings in the vars/controller.yaml:

```yaml
controller_host_name: "<account-name>.saas.appdynamics.com"
controller_port: "443"
controller_account_name: "<account-name>" # Please add this to your Vault
controller_account_access_key: "<access-key>" # Please add this to your Vault
enable_ssl: "TRUE"
controller_global_analytics_account_name: '<account-name>' # Please add this to your Vault
agent_log_level: "info" # info | trace | debug | warn | error | fatal | all | off
```
## Add your list of hosts with Java agents in inventory.ini:

```ini
[linux]
target1-ubuntu ansible_host=<ip address> ansible_user=<username> ansible_become_pass=<password>
```

## Add your agent upgrade information in agent_upgrade.yaml:

```yaml
---
- hosts: all
  tasks:
    - name: Include variables for the controller settings
      include_vars: vars/controller.yaml
    - include_role:
        name: appdynamics.agents.java
        # use java role variables in the following instrumentation tasks when public: yes
        public: yes
      vars:
        agent_version: <agent version>
        agent_type: java


    - include_role:
        name: appdynamics.agents.instrument_tomcat
      vars:
      # instrument tomcat:
        tomcat_service: <tomcat version>
        application_name: "<application name>"
        tier_name: "<tier name>"
        app_user: <app user>
        restart_app: yes
        tomcat_config: <path to tomcat setenv.sh>
```

# Usage

Once the controller, inventory, and agent upgrade information has been added, the upgrade can be run via the following command:

```
ansible-playbook -i vars/inventory.ini agent_upgrade.yaml
```

The playbook can be used to rollback agents to a previous version, by changing the agent_version variable in agent_upgrade.yaml. Future agent upgrades can be done via simply changing the agent version to the desired release. 

# Screenshots

![/IMAGES/0image.png](/IMAGES/0image.png)

### LICENSE

Provided under Cisco Sample Code License, for details see [LICENSE](LICENSE.md)

### CODE_OF_CONDUCT

Our code of conduct is available [here](CODE_OF_CONDUCT.md)

### CONTRIBUTING

See our contributing guidelines [here](CONTRIBUTING.md)

#### DISCLAIMER:
<b>Please note:</b> This script is meant for demo purposes only. All tools/ scripts in this repo are released for use "AS IS" without any warranties of any kind, including, but not limited to their installation, use, or performance. Any use of these scripts and tools is at your own risk. There is no guarantee that they have been through thorough testing in a comparable environment and we are not responsible for any damage or data loss incurred with their use.
You are responsible for reviewing and testing any scripts you run thoroughly before use in any non-testing environment.