# ansible-playbooks

- [Requirements](#requirements)
- [Installation](#installation)
- [Known Limitation](#known-limitation)

---

## Requirements
* An App Host (Edge Gateway) for `fn_ansible` app
* A Linux machine for running ansible playbooks
  * You may use App Host as a Linux machine if you can add SSH public key. `fn_ansible` on App Host does not support running playbooks inside the container, so you need SSH connection from the App Host container to a Linux machine to run playbooks. Default `fn_ansible` image does not come with SSH password support so SSH key setup is needed.
  

---

## Installation
Please follow the `fn_ansible` app installation steps on the App Host. There is nothing special for the app.config settings.

You'll need to obtain CP4s platform (not SOAR) API key and secret, as well as SSH private and public keys.

### Files to add to the fn_ansible

The files needed by this package is the following.

| File Name              | File Path                          | File Type  | Remarks                                                      |
| ---------------------- | ---------------------------------- | ---------- | ------------------------------------------------------------ |
| hosts                  | /var/rescircuits/ansible/inventory | Plain Text | host information which runs ansible playbooks                |
| ssh_key                | /var/rescircuits/ansible/env       | Plain Text | SSH private key                                              |
| variables.yml          | /var/rescircuits/ansible/project   | YAML       | Configuration Variables                                      |
| secrets.yml            | /var/rescircuits/ansible/project   | YAML       | API key and secret                                           |
| check_de_health.yml    | /var/rescircuits/ansible/project   | YAML       | Ansible playbook to health check the Data Explorer (called from SOAR playbook) |
| check_tii_health.yml   | /var/rescircuits/ansible/project   | YAML       | Ansible playbook to health check the Threat Intelligence Insights (called from SOAR playbook) |
| list_open_offenses.yml | /var/rescircuits/ansible/project   | YAML       | Ansible playbook to health check the QRadar SIEM offenses (called from SOAR playbook) |

### hosts File

The following is the example.

```
ldap ansible_host=192.168.65.8 ansible_user=root
apphost ansible_host=192.168.65.4 ansible_user=integration
```

First column is the name referenced from the playbooks and `apphost` is used by default. No root privilege is required on the Linux machine for this integration.

If you change the host name in the hosts file, you'll also need to update `hosts:` value in `enable_ti_mock.yml` and `populate_ti_mock.yml` files.

### ssh_key File

You'll find an information on how to generate SSH private and public key pair on Linux machine by using Google search.

You can copy the private key to the `fn_ansible` and append the public key to the target account on the playbook running Linux machine. Do not protect the private key with password.

### variables.yml File

Define the values specific to your environment. If you experience a security warning, change the `validate_certs` to false.

```
# Variables for QRadar SIEM
qradar_server_uri: "https://your_qradar_host_name_or_ip"

# Variables for CP4S
cp4s_server_uri: "https://your_cp4s_host_name"

# common for both QRadar and CP4S
validate_certs: true
#validate_certs: false
```

### secrets.yml File

You need to obtain CP4s platform (not SOAR) API key and secret for this integration.

```
# Secrets for QRadar SIEM
qradar_token: "place your qradar auth token here"

# Secrets for CP4S
cp4s_api_key: "place your cp4s platform (not soar) api key here"
cp4s_api_pw: "place your cp4s platform (not soar) api password here"
```

## Known Limitation
Unlike app.config, there is no easy way to hide secrets in the yaml file.
