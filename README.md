# CP4S Health check Playbooks

- [Overview](#overview)
- [Requirements](#requirements)
- [Installation](#installation)
- [Running a health check](#running-a-health-check)

---

## Overview
This is a health check package for CP4S SOAR to monitor Technology Showcase demonstration components such as:

- QRadar SIEM
- Data Explorer
- Threat Intelligence Insithgs
- LDAP

The health check results will be stored in the case (Incident) variables. One case creation is required per one health check iteration.

---

## Requirements
* CP4S 1.10.7+
* An App Host (Edge Gateway) for `fn_ansible` app
* A Linux machine for running ansible playbooks
  * You may use App Host as a Linux machine if you can add SSH public key. `fn_ansible` on App Host does not support running playbooks inside the container, so you need SSH connection from the App Host container to a Linux machine to run playbooks. Default `fn_ansible` image does not come with SSH password support so SSH key setup is needed.
  

---

## Installation
### Import SOAR Playbooks

Follow the steps described in the [soar-playbooks](./soar-playbooks) folder.

### Import Ansible Playbooks

Follow the guide in the [ansible-playbooks](./ansible-playbooks) folder.

---

## Running a health check
Health check is triggered by the `is_health_check` property set to True. This can be done from the manual playbook (menu item) named `Healthcheck: START` or via SOAR REST API.

Results are stored in the `healthcheck_results_1`, `healthcheck_results_2`, `healthcheck_results_3` and `healthcheck_results_4` properties in a JSON format:

{
	"index": *number from 1 to 4*,
	"summary": "successful",
	"content": {...}, 
	"execution_time_ms": *msec_number*, 
	"last_update_epoc_ms": *msec_number*
}

| index | component                    | What is checked/returned                                     |
| ----- | ---------------------------- | ------------------------------------------------------------ |
| 1     | QRadar SIEM                  | Recent offenses                                              |
| 2     | Data Explorer                | Status returned from the DE API endpoint                     |
| 3     | Threat Intelligence Insights | Checks if `Ryuk Ransomware Infection Chain` threat information is available |
| 4     | LDAP                         | Checks if a specific user is found                           |

