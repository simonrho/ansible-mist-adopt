# Adopt Juniper Devices Playbook

This Ansible playbook automates the adoption and configuration of Juniper devices using the Mist API.

## Prerequisites

1. Install Ansible and required collections:
```bash
pip install ansible
ansible-galaxy collection install junipernetworks.junos ansible.netcommon
```
2. Ensure your inventory file is set up with the necessary devices and variables. An example inventory file (`inventory.ini`) is provided:

```ini
[all]
device1 ansible_host=192.168.1.10 ansible_user=admin ansible_ssh_pass=ExampleP@ss1 org_id=example_org_id site_id=""
device2 ansible_host=192.168.1.20 ansible_user=admin ansible_ssh_pass=ExampleP@ss2 org_id=example_org_id site_id=""

[all:vars]
ansible_become=yes
ansible_connection=ansible.netcommon.netconf
ansible_network_os=junipernetworks.junos.junos
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'
mist_api_key=ExampleAPIKey1234
```

## Mist API Key
Steps to obtain the MIST API key.
1. Log in to your Mist account.
2. Select the desired organization. 
3. Navigate to organization settings. 
4. Find the API Token section. 
5. Click "Create Token."
6. Choose the appropriate access level. 
7. Set site access preferences. 
8. Generate and securely store the API key.

## Usage

Run the playbook with the provided inventory file:

```bash
ansible-playbook -i inventory.ini adopt.yaml
```
This playbook will:

1. Retrieve the SSH commands from the Mist API for the specified organization and site.
2. Execute the SSH commands to adopt and configure the Juniper devices.
3. Display a success message if the device is successfully adopted and configured.

## Optional Variables

- `delete_phone_home`: Set this variable to `false` if you want to keep the 'delete system phone-home' command in the SSH commands. By default, this command is removed from the SSH commands.

Example:

```bash
ansible-playbook -i inventory.ini adopt.yaml -e delete_phone_home=false
```

## Run example
```bash
ansible-playbook -i inventory.ini adopt.yaml 

PLAY [Adopt Juniper devices] *******************************************************************************************************************************************************

TASK [Get Mist API response] *******************************************************************************************************************************************************
ok: [device1 -> localhost]
ok: [device2 -> localhost]

TASK [Extract SSH commands from Mist API response] *********************************************************************************************************************************
ok: [device1]
ok: [device2]

TASK [Remove 'delete system phone-home' command if delete_phone_home is true] ******************************************************************************************************
ok: [device1]
ok: [device2]

TASK [Adopt and configure Juniper device] ******************************************************************************************************************************************
changed: [device2]
changed: [device1]

TASK [Exit with result] ************************************************************************************************************************************************************
ok: [device1] => {
    "msg": "Successfully adopted and configured the Juniper device."
}
ok: [device2] => {
    "msg": "Successfully adopted and configured the Juniper device."
}

PLAY RECAP *************************************************************************************************************************************************************************
device1                    : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
device2                    : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```