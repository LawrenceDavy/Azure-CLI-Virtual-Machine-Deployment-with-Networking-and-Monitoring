Below is a professional `README.md` file formatted in Markdown for your GitHub repository. It outlines a step-by-step guide using the Azure CLI to deploy a virtual machine with networking and monitoring, aligned with the AZ-900 (Microsoft Azure Fundamentals) learning objectives. The content is based on the provided Notion guide, expanded with detailed explanations, and includes placeholders for screenshots to enhance visual clarity for potential employers. Each step is designed to demonstrate your technical proficiency, attention to detail, and ability to document processes effectively.

---

# Azure CLI Virtual Machine Deployment with Networking and Monitoring

![Project Banner](https://via.placeholder.com/1200x300.png?text=Azure+CLI+VM+Deployment+Project)
*Placeholder: Replace with a custom banner showcasing Azure CLI and VM deployment visuals.*

This repository provides a comprehensive, step-by-step guide to deploying a virtual machine (VM) in Microsoft Azure using the Azure Command-Line Interface (CLI). It includes configuring networking components and enabling monitoring, aligning with the **AZ-900 Microsoft Azure Fundamentals** learning objectives. This project is designed to showcase proficiency in Azure core services, CLI usage, networking, and monitoring setup—ideal for demonstrating cloud skills to potential employers.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Step-by-Step Guide](#step-by-step-guide)
   - [Step 1: Set Up Your Azure Environment](#step-1-set-up-your-azure-environment)
   - [Step 2: Create a Resource Group](#step-2-create-a-resource-group)
   - [Step 3: Create a Virtual Network (VNet) and Subnet](#step-3-create-a-virtual-network-vnet-and-subnet)
   - [Step 4: Create a Network Security Group (NSG)](#step-4-create-a-network-security-group-nsg)
   - [Step 5: Deploy the Virtual Machine](#step-5-deploy-the-virtual-machine)
   - [Step 6: Enable Monitoring with Azure Monitor](#step-6-enable-monitoring-with-azure-monitor)
   - [Step 7: Test Connectivity](#step-7-test-connectivity)
   - [Step 8: Clean Up Resources](#step-8-clean-up-resources)
4. [AZ-900 Learning Objectives Covered](#az-900-learning-objectives-covered)
5. [Troubleshooting](#troubleshooting)
6. [Contributing](#contributing)
7. [License](#license)

---

## Project Overview

This project demonstrates how to use the Azure CLI to:
- Provision a resource group and virtual network with a subnet.
- Configure a network security group (NSG) for secure access.
- Deploy a Linux VM (Ubuntu 20.04 LTS).
- Set up basic monitoring using Azure Monitor.
- Test connectivity via SSH.
- Clean up resources to avoid unnecessary costs.

The guide is based on the [Notion Project 2 Guide](https://www.notion.so/Project-2-Deploy-a-Virtual-Machine-with-Networking-and-Monitoring-1bcd7770c29d808da9b2d7e9d1c45907) and tailored to reflect AZ-900 competencies, such as understanding Azure core services, networking, and monitoring.

---

## Prerequisites

Before starting, ensure you have the following:
- **Azure Subscription**: Sign up for a [free Azure account](https://azure.microsoft.com/free/) if you don’t have one.
- **Azure CLI**: Installed locally or accessible via Azure Cloud Shell. [Install instructions](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
- **SSH Client**: For Linux/Mac, use the terminal; for Windows, use PuTTY or Windows Terminal.
- **Text Editor**: For scripting (e.g., VS Code).
- **Basic CLI Knowledge**: Familiarity with command-line syntax.

---

## Step-by-Step Guide

### Step 1: Set Up Your Azure Environment

1. **Install Azure CLI** (if not using Cloud Shell):
   - Download and install from [Microsoft's official guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
   - Verify installation:
     ```bash
     az --version
     ```
   ![Image](https://github.com/user-attachments/assets/88d3d5a5-4bf8-4f2d-b8d5-df67dcadaa3a)

2. **Log in to Azure**:
   - Run the following command and follow the browser prompt to authenticate:
     ```bash
     az login
     ```
  ![Image](https://github.com/user-attachments/assets/e608ded9-8afc-45fa-a2ae-3a254d23d502)


---

### Step 2: Create a Resource Group

A resource group is a logical container for Azure resources.

1. **Create the Resource Group**:
   - Command:
     ```bash
     az group create --name "MyResourceGroup" --location "eastus"
     ```
   - Explanation: `eastus` is chosen for proximity (adjust based on your region). Replace `MyResourceGroup` with a unique name.
  ![Image](https://github.com/user-attachments/assets/a993b7bf-6a78-4c25-a4b9-c347558bd82c)
---

### Step 3: Create a Virtual Network (VNet) and Subnet

Networking is critical for VM communication.

1. **Create a Virtual Network**:
   - Command:
     ```bash
     az network vnet create \
       --resource-group "MyResourceGroup" \
       --name "MyVNet" \
       --address-prefix "10.0.0.0/16" \
       --location "eastus"
     ```
   - Explanation: The address prefix defines the VNet’s IP range.
   ![Image](https://github.com/user-attachments/assets/8da642c2-52b1-4d96-baa4-2aa37ce0c8d5)

2. **Create a Subnet**:
   - Command:
     ```bash
     az network vnet subnet create \
       --resource-group "MyResourceGroup" \
       --vnet-name "MyVNet" \
       --name "MySubnet" \
       --address-prefix "10.0.1.0/24"
     ```
   - Explanation: The subnet is a smaller IP range within the VNet for the VM.
  ![Image](https://github.com/user-attachments/assets/28e349c5-a770-4f14-8d76-6ee0a4a87e8f)

---

### Step 4: Create a Network Security Group (NSG)

An NSG controls inbound and outbound traffic.

1. **Create the NSG**:
   - Command:
     ```bash
     az network nsg create \
       --resource-group "MyResourceGroup" \
       --name "MyNSG" \
       --location "eastus"
     ```
  ![Image](https://github.com/user-attachments/assets/8f93edf6-4868-4a14-ba4e-dfebbfdf7406)
  ![Image](https://github.com/user-attachments/assets/4a7eb15a-9c0e-450c-9de5-cef3d943b33d)

2. **Add a Rule to Allow SSH**:
   - Command:
     ```bash
     az network nsg rule create \
       --resource-group "MyResourceGroup" \
       --nsg-name "MyNSG" \
       --name "AllowSSH" \
       --protocol "Tcp" \
       --priority 1000 \
       --destination-port-range "22" \
       --access "Allow" \
       --direction "Inbound"
     ```
   - Explanation: Port 22 is opened for SSH access.
  ![Image](https://github.com/user-attachments/assets/50501cc3-daba-44bc-8ef4-838af9b84f0b)

3. **Associate NSG with Subnet**:
   - Command:
     ```bash
     az network vnet subnet update \
       --resource-group "MyResourceGroup" \
       --vnet-name "MyVNet" \
       --name "MySubnet" \
       --network-security-group "MyNSG"
     ```
   - ![image](https://github.com/user-attachments/assets/0c61d0a7-a92e-4192-971f-b5fc0adae1d9)


---

### Step 5: Deploy the Virtual Machine

Deploy a VM with a public IP for external access.

1. **Create the VM**:
   - Command:
     ```bash
     az vm create \
       --resource-group "MyResourceGroup" \
       --name "MyUbuntuVM" \
       --image "UbuntuLTS" \
       --admin-username "azureuser" \
       --generate-ssh-keys \
       --vnet-name "MyVNet" \
       --subnet "MySubnet" \
       --public-ip-sku "Standard" \
       --location "eastus"
     ```
   - Explanation: This creates an Ubuntu VM, auto-generates SSH keys, and assigns a public IP.
   - ![Image](https://github.com/user-attachments/assets/b6f87739-2c05-4236-8bf0-7ef6b7c2c3a6)

2. **Retrieve Public IP**:
   - Command:
     ```bash
     az vm show \
       --resource-group "MyResourceGroup" \
       --name "MyUbuntuVM" \
       --show-details \
       --query "publicIps" \
       --output tsv
     ```
   - ![image](https://github.com/user-attachments/assets/c688f6ef-3a69-4264-abee-03e82704cbdf)


---

### Step 6: Enable Monitoring with Azure Monitor

Monitoring ensures VM performance visibility.

1. **Enable Boot Diagnostics**:
   - Command:
     ```bash
     az vm boot-diagnostics enable \
       --resource-group "MyResourceGroup" \
       --name "MyUbuntuVM"
     ```
   - Explanation: Captures boot logs for troubleshooting.
   - *Screenshot Placeholder*: Show the terminal output confirming enablement.

2. **Install Azure Monitor Agent** (optional, requires extension):
   - Command:
     ```bash
     az vm extension set \
       --resource-group "MyResourceGroup" \
       --vm-name "MyUbuntuVM" \
       --name "AzureMonitorLinuxAgent" \
       --publisher "Microsoft.Azure.Monitor" \
       --version "1.0"
     ```
   - Explanation: Collects performance metrics (CPU, memory, etc.).
   - *Screenshot Placeholder*: Capture the terminal output confirming agent installation.

---

### Step 7: Test Connectivity

Verify the VM is accessible.

1. **SSH into the VM**:
   - Command (replace `<PUBLIC_IP>` with the IP from Step 5):
     ```bash
     ssh azureuser@<PUBLIC_IP>
     ```
   - Type `yes` to accept the host key, then log in.
   - *Screenshot Placeholder*: Show the terminal after successful SSH login (e.g., Ubuntu welcome message).

2. **Run a Test Command**:
   - Inside the VM:
     ```bash
     hostname
     ```
   - *Screenshot Placeholder*: Capture the terminal output showing the VM’s hostname.

3. **Exit the VM**:
   - Command:
     ```bash
     exit
     ```

---

### Step 8: Clean Up Resources

Avoid unnecessary costs by deleting resources.

1. **Delete the Resource Group**:
   - Command:
     ```bash
     az group delete --name "MyResourceGroup" --yes --no-wait
     ```
   - Explanation: `--yes` skips confirmation; `--no-wait` runs in the background.
   - *Screenshot Placeholder*: Show the terminal output confirming deletion initiation.

---

## AZ-900 Learning Objectives Covered

This project aligns with AZ-900 domains:
- **Cloud Concepts**: Demonstrates Azure’s IaaS model with VMs.
- **Core Azure Services**: Uses resource groups, VMs, VNets, and NSGs.
- **Security, Privacy, Compliance, and Trust**: Configures NSGs for secure access.
- **Azure Pricing and Support**: Highlights resource cleanup to manage costs.
- **Management Tools**: Leverages Azure CLI for automation and control.

---

## Troubleshooting

- **Login Fails**: Ensure `az login` succeeded and the correct subscription is set.
- **SSH Connection Refused**: Verify NSG rules allow port 22 and the public IP is correct.
- **Resource Creation Errors**: Check region availability and naming uniqueness.

---

## Contributing

Contributions are welcome! Please:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/addition`).
3. Commit changes (`git commit -m "Add feature"`).
4. Push to the branch (`git push origin feature/addition`).
5. Open a pull request.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

### Notes for Implementation
- **Screenshots**: Take each screenshot as described in the placeholders. Upload them to the `screenshots/` folder in your GitHub repo and update the README with actual links (e.g., `![Step 1 Output](screenshots/step1-output.png)`).
- **Polish**: Add a `.github/workflows/` directory with a simple CI/CD pipeline (e.g., linting Markdown) to impress employers with automation skills.
- **Customization**: Replace placeholder names (e.g., `MyResourceGroup`) with a unique naming convention tied to your personal brand (e.g., `JDoeRG2025`).

This README is professional, detailed, and showcases your ability to document technical processes effectively—perfect for a portfolio aimed at future employers!
