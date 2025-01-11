import os
import csv
import json
import yaml
import requests
from datetime import datetime
from pysnmp.hlapi import *
from colorama import Fore, Style, init
import schedule
import time

# Initialize Colorama
init(autoreset=True)

# CSV Template Fields
csv_template = [
    {"field": "Device Name", "description": "The name of the device"},
    {"field": "IP Address", "description": "The IP address of the device"},
    {"field": "Model", "description": "The model of the device"},
    {"field": "Location", "description": "The physical location of the device"}
]

# Export Validation Results to CSV
def export_validation_results(results, output_file="validation_results.csv"):
    try:
        with open(output_file, mode="w", newline="") as file:
            writer = csv.DictWriter(file, fieldnames=results[0].keys())
            writer.writeheader()
            writer.writerows(results)
        print(Fore.GREEN + f"[INFO] Validation results exported to {output_file}")
    except Exception as e:
        print(Fore.RED + f"[ERROR] Failed to export results: {str(e)}")

# Export Summary to JSON
def export_summary_to_json(data, output_file="device_summary.json"):
    try:
        with open(output_file, "w") as file:
            json.dump(data, file, indent=4)
        print(Fore.GREEN + f"[INFO] Summary exported to {output_file}")
    except Exception as e:
        print(Fore.RED + f"[ERROR] Failed to export summary: {str(e)}")

# Protocol Validation
def validate_protocols(device):
    protocols = {
        "BGP": "show bgp summary",
        "OSPF": "show ip ospf neighbor",
        "LLDP": "show lldp neighbors",
        "NTP": "show ntp associations",
        "VRRP": "show vrrp",
        "STP": "show spanning-tree",
        "DNS": "nslookup example.com",
        "HTTP": "curl -I http://example.com"
    }
    results = {}
    for protocol, command in protocols.items():
        try:
            # Simulate running the command (replace with actual SSH command execution)
            print(Fore.CYAN + f"[INFO] Running {command} on {device['name']} ({device['ip']})")
            results[protocol] = f"Output of {command} on {device['name']}"
        except Exception as e:
            print(Fore.RED + f"[ERROR] Failed to run {command}: {str(e)}")
            results[protocol] = "Failed"
    return results

# Generate Device Summary
def generate_device_summary(devices):
    summary = []
    for device in devices:
        print(Fore.CYAN + f"[INFO] Generating summary for {device['name']} ({device['ip']})...")
        summary.append({
            "Device Name": device["name"],
            "IP Address": device["ip"],
            "Protocols": validate_protocols(device)
        })
    return summary

# Create CSV Input Based on Template
def create_csv_input(template=csv_template, output_file="device_input.csv"):
    """
    This function prompts the user for data based on the template and generates a CSV file.
    """
    # Prepare the headers based on the template
    headers = [field['field'] for field in template]
    
    # Initialize a list to store the device data
    devices_data = []
    
    # Prompt the user for each field
    print(Fore.CYAN + "[INFO] Enter device details (leave blank to skip):")
    while True:
        device_data = {}
        for field in template:
            user_input = input(f"{field['description']} ({field['field']}): ").strip()
            if user_input:
                device_data[field['field']] = user_input
        
        if device_data:
            devices_data.append(device_data)
        
        # Ask if the user wants to add another device
        add_more = input("\nWould you like to add another device? (y/n): ").strip().lower()
        if add_more != 'y':
            break

    # Write the device data to a CSV file
    try:
        with open(output_file, mode="w", newline="") as file:
            writer = csv.DictWriter(file, fieldnames=headers)
            writer.writeheader()
            writer.writerows(devices_data)
        print(Fore.GREEN + f"[INFO] CSV file '{output_file}' created successfully!")
    except Exception as e:
        print(Fore.RED + f"[ERROR] Failed to create CSV file: {str(e)}")

# Push to ELK/Grafana
def push_to_dashboard(data, elk_url="http://localhost:9200/devices"):
    try:
        for entry in data:
            response = requests.post(elk_url, json=entry)
            if response.status_code == 201:
                print(Fore.GREEN + f"[INFO] Data pushed to ELK for {entry['Device Name']}")
            else:
                print(Fore.RED + f"[ERROR] Failed to push data for {entry['Device Name']}: {response.text}")
    except Exception as e:
        print(Fore.RED + f"[ERROR] ELK push failed: {str(e)}")

# Automate Checks with Scheduler
def scheduled_device_checks(devices):
    print(Fore.CYAN + "[INFO] Running scheduled device checks...")
    results = []
    for device in devices:
        result = {
            "Device Name": device["name"],
            "IP Address": device["ip"],
            "Reachable": is_reachable(device["ip"]),
            "Protocols": validate_protocols(device)
        }
        results.append(result)
    export_validation_results(results)
    summary = generate_device_summary(devices)
    export_summary_to_json(summary)
    push_to_dashboard(summary)

# Ping Test for Reachability
def is_reachable(ip):
    response = os.system(f"ping -c 1 {ip} > /dev/null 2>&1")
    return response == 0

# SNMP Trap Receiver (simulated function)
def snmp_trap_receiver():
    print(Fore.CYAN + "[INFO] Starting SNMP Trap Receiver...")
    # Add your SNMP trap logic here

# Create Ansible Inventory
def create_ansible_inventory(devices):
    inventory = {
        "all": {
            "hosts": [device['name'] for device in devices],
            "vars": {
                "ansible_user": "admin",
                "ansible_password": "password"
            }
        }
    }
    with open("ansible_inventory.yml", "w") as file:
        yaml.dump(inventory, file)
    print(Fore.GREEN + "[INFO] Ansible inventory file created.")

# Run Ansible Playbook
def run_ansible_playbook(playbook):
    print(Fore.CYAN + f"[INFO] Running Ansible playbook: {playbook}")
    os.system(f"ansible-playbook {playbook}")

# Terraform Runner (simulated)
def run_terraform(command):
    print(Fore.CYAN + f"[INFO] Running Terraform {command}...")
    os.system(f"terraform {command}")

# Show the Menu
def show_menu():
    print("\nMenu:")
    print("1. Validate Protocols")
    print("2. Fetch Device Logs")
    print("3. Export Device Summary")
    print("4. Start SNMP Trap Receiver")
    print("5. Create Ansible Inventory")
    print("6. Run Ansible Playbook")
    print("7. Run Terraform")
    print("8. Create CSV Input Based on Template")
    print("0. Exit")

# Main Program
def main():
    devices = [
        {"name": "Switch1", "ip": "192.168.1.1"},
        {"name": "Router1", "ip": "192.168.1.2"}
    ]
    
    while True:
        show_menu()
        choice = input("\nEnter your choice: ").strip()

        if choice == "1":
            results = []
            for device in devices:
                protocols = validate_protocols(device)
                results.append({"Device Name": device["name"], **protocols})
            export_validation_results(results)

        elif choice == "2":
            summary = generate_device_summary(devices)
            export_summary_to_json(summary)

        elif choice == "3":
            summary = generate_device_summary(devices)
            export_summary_to_json(summary)

        elif choice == "4":
            snmp_trap_receiver()

        elif choice == "5":
            create_ansible_inventory(devices)

        elif choice == "6":
            playbook = input("Enter playbook filename: ").strip()
            run_ansible_playbook(playbook)

        elif choice == "7":
            command = input("Enter Terraform command (init, apply, plan, destroy): ").strip()
            run_terraform(command)

        elif choice == "8":
            # Call the CSV input creation function
            create_csv_input()

        elif choice == "0":
            print("[INFO] Exiting the program. Goodbye!")
            break

        else:
            print("[ERROR] Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
