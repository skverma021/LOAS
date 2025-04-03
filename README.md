# LOAS
Logon As a Service: Added Account and Group Detail

Explanation:

---: YAML document start.
- name: ...: Defines a playbook.
hosts: all: Targets all hosts in your inventory.
gather_facts: true: Collects system information (including OU details for Windows).
vars:: Defines variables for easier management.
local_group_name: The name of the local group to manage.
account_to_add: The account to add to the local group.
security_policy_name: The name of the "Logon As a Service" user right.
csv_file_path: The path where the CSV file will be saved (adjust as needed).
csv_header: The header row for the CSV file.
tasks:: Defines the list of tasks to be executed on the target hosts.
win_security_policy (Get): Retrieves the current users/groups assigned to the "Logon As a Service" right and registers the output in logon_as_service_policy.
win_group (Check Existence): Checks if the local group WF_URA_LOAS exists and registers the output in local_group_info.
win_group (Create): Creates the local group if it doesn't exist based on the local_group_info.exists condition.
win_group_members (Add Member): Adds the NT SERVICE\himds account to the local group if the group was just created or if the account is not already a member.
win_security_policy (Add Group): Adds the local group WF_URA_LOAS to the "Logon As a Service" right if it's not already present.
set_fact (Prepare CSV Data): Creates Ansible facts to store the relevant information for each host in a format suitable for the CSV. It handles cases where OU details or group members might be missing.
lineinfile (Append to CSV): Appends a line containing the hostname, OU details, "Logon As a Service" principals, local group existence status, and local group members to the specified CSV file.
delegate_to: localhost: Executes this task on the Ansible control node.
run_once: true: Ensures this task is executed only once after processing all hosts to avoid duplicate entries in the CSV.
create: true: Creates the CSV file if it doesn't exist.
debug (Display Table): Prints the collected information in a tabular format on the Ansible control node.
delegate_to: localhost: Executes this task on the Ansible control node.
run_once: true: Ensures the table is printed only once.
The msg uses Jinja2 templating to format the output into a table.
Before Running:

Ensure WinRM is configured on the target Windows hosts.
Ensure you have the necessary credentials configured in your Ansible inventory or provided during playbook execution to manage the target hosts and their security policies and local groups.
Adjust the csv_file_path variable if you want to save the CSV file in a different location.
How to Run:

Save the YAML code as a .yml file (e.g., logon_service_config.yml).

Run the playbook from your Ansible control node using the following command:

Bash

ansible-playbook -i <your_inventory_file> logon_service_config.yml
Replace <your_inventory_file> with the path to your Ansible inventory file.

Output:

After the playbook runs successfully, you will:

Have a CSV file (/tmp/logon_as_service_info.csv or your specified path) containing the information for each host.
See a tabular output in your Ansible terminal displaying the gathered information for all managed hosts. The table will show the hostname, OU, accounts/groups in "Logon As a Service", whether the local group exists, and its members.
