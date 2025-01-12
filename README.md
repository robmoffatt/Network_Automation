# Network_Automation

Script by Robert Moffatt

## Overview

Network Automation is designed to automate various network management and infrastructure tasks.

- Validate network protocols

  - BGP
  - OSPF
  - HTTP
  - etc...

- Fetch device logs for analysis
- Export device summaries and validation results
- Start an SNMP Trap Reciever
- Create an Ansible inventory file
- Run Ansible playbooks
- Run Terraform commands
- Generate device input CSV files based on a template

Interactible script via a menu-based interface to choose which operation to perform. Each option in the menu corresponds to a specific function in the script.

## Script usage and Breakdown

### 1. Validate Protocols

- Validates the common network protocols for each device in the device list
- Script simulates running the protocols via their common commands to output the results
- Results are saved in a CSV file `validation_results.csv`

### 2. Fetch Device Logs

- Generates a summary of the device status by running protocol checks and collecting logs for each device
- Exports device summary to a JSON file `device_summary.json`
- Main use-case: troubleshooting and monitoring device status

### 3. Export Device Summary

- Generates a summary of all devices and saves it as a JSON file

  - Name
  - IP
  - Protocols

- Use-case: tracking and reviewing device configurations

### 4. Start SNMP Trap Reciever

- Simulation of SNMP, not full SNMP service

### 5. Create Ansible Inventory

- Creates an Ansible inventory file `ansible_inventory.yml` that includes the list of devices to manage
- It includes default variables like `ansible_user` and `ansible_password`
- Ansible is a powerful automation tool that is used to configure and manage systems

### 6. Run Ansible Playbook

- Runs an Ansible playbook based on the filename provided by the user
- Ansible playbooks are YAML files containing instructions for automation tasks (e.g., configure devices, deploy applications)

### 7. Run Terraform

- Run Terraform commands such as `init`, `apply`, `plan`, and `destroy`
- Used for automating infrastructure provisioning and management (e.g., creating cloud resources, managing server configurations)
- User dictates which command to run

### 8. Create CSV Input Based on Template

- Allows the user to generate a CSV file that contains device information based on a predefined template
- The template asks for `device name`, `IP address`, `model`, and `location`
- User is prompted to enter this information for each device, and the details are saved in `device_input.csv`

### Exit

- Gives an option to exit the program

## Using the Script

### Brief walkthrough and tips to get started

Ensure that Python is installed and all required packages are installed on the script host:

- `requests`, `colorama`, `pyyaml`, `pysnmp`
- install these packages via: `pip install <package_name>`

#### 1. Run the script

- headless running: `sudo chmod +x <python_script.py>`, then `python <python_script.py>`
- **NOTE:** some OS' may require you to run python with `sudo` permissions or via `python3 python_script.py>`

#### 2. Select an option from the Menu

- After selection of `1 - 8` or `0` the menu option will run

#### 3. Following the prompts

- As mentioned above you may need to enter files names or information to build files for each option

#### 4. Results

- Depending on what was entered, files will be generated in the local folder that the command was run in

#### Conclusion

This script is a multi-functional tool for network and infrastructure management. By providing automated validation, log fetching, and infrastructure provisioning features, it simplifies network management tasks and integrates with tools like Ansible and Terraform. The addition of a CSV input generator and SNMP Trap Receiver makes it adaptable for a wide range of environments.
