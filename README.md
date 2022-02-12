# VM-Series Next-Generation Firewall (BYOL) on IBM Cloud VPC (Standalone; High Resiliency (Active/Active) planned)

IBM Cloud is an important deployment platform for your business-critical applications. Protecting the increased public cloud footprint from threats, data loss, and business disruption remains challenging. The VM-Series Virtual Next-Generation Firewall (NGFW) on IBM Cloud solves these challenges, enabling you to:
Protect your workloads through unmatched application visibility and precise control.
Prevent threats from moving laterally between workloads and stop data exfiltration.
Eliminate security-induced application development bottlenecks with automation and centralized management.

Palo Alto Networks VM-Series virtual NGFWs protect your IBM Cloud workloads with next-generation security features that allow you to confidently and quickly migrate your business-critical applications to the cloud. Embed the VM-Series in your automated infrastructure provisioning workflows to prevent data loss and business disruption in even the most dynamic environments.

https://www.paloaltonetworks.com


## Version
PAN-OS 9.1.3
PAN-OS 10.0.6

Follow the standard upgrade process after the VM-series in installed if a newer release is required.

 https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/about-the-vm-series-firewall/upgrade-the-vm-series-firewall/upgrade-the-vm-series-model.html


## License Requirements
The VM-Series on IBM Cloud supports bring-your-own-license (BYOL) as a licensing model. You can purchase your VM-Series license through normal Palo Alto Networks channels, and then deploy the VM-Series using the license authorization code you received. 
https://docs.paloaltonetworks.com/vm-series/10-0/vm-series-deployment/license-the-vm-series-firewall/vm-series-models/license-typesvm-series-firewalls

https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/license-the-vm-series-firewall.html

## Prerequisites
- Have access to [IBM Cloud Gen 2 VPC](https://cloud.ibm.com/vpc-ext/).
- A VPC with at least two subnets and one IP address unassigned in each subnet - the VM-Series VSI will be assigned the IP Addresses in its interfaces from the user provided subnets as the input. [Reference](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet)
- Identify region to install PAN-OS
    - us-east
    - us-south
    - ca-tor
    - eu-gb
    - eu-de
    - au-syd
    - jp-osa
    - jp-tok
    
## Dependencies

Before you can apply the template in IBM Cloud, complete the following steps.


1.  Ensure that you have the following permissions in IBM Cloud Identity and Access Management:
    * `Manager` service access role for IBM Cloud Schematics
    * `Operator` platform role for VPC Infrastructure
2.  Ensure the following resources exist in your VPC Gen 2 environment
    - VPC ()
    - SSH Key: [Public SSH Key Doc](https://cloud.ibm.com/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-ssh-keys)
    - VPC has 2 subnets
    - Floating IP (FIP) Address to assign to the management interface of VM-Series instance post deployment. FIP is used to access your VPC virtual server instance over the public internet. https://cloud.ibm.com/docs/vpc?topic=vpc-creating-a-vpc-using-the-rest-apis#create-floating-ip-api-tutorial


### Required Parameters for Deployment
Fill in the following values, based on the steps that you completed before you began.

| Key | Definition | Value Example |
| --- | ---------- | ------------- |
| `image_name` | The VM-Series image to be installed. | "pa-vm-kvm-9-1-3-1 or pa-vm-kvm-10-0-6" |
| `region` | The VPC region that you want your VPC virtual servers to be provisioned. | "us-east us-south ca-tor eu-gb eu-de au-syd jp-osa jp-tok" |
| `vnf_profile` | The profile of compute CPU and memory resources to be used when provisioning the vnf instance. To list available profiles, run `ibmcloud is instance-profiles`or https://cloud.ibm.com/docs/vpc?topic=vpc-profiles. | "bx2-8x32" |
| `subnet_id1` | The ID of the subnet(management) which will be associated with first interface of the VNF instance. Click on the subnet details in the VPC Subnet Listing to determine this value | "0717-xxxxxx-xxxx-xxxxx-8fae-xxxxx" |
| `subnet_id2` | The ID of the subnet(dataplane) which will be associated with second interface of the VNF instance. Click on the subnet details in the VPC Subnet Listing to determine this value | "0717-xxxxxx-xxxx-xxxxx-8fae-xxxxx" |
| `vnf_security_group` | The name of the security group to which the VNF Instance's first interface(management) belong to | "vm-series-mgmt-sg" |
| `vnf_instance_name` | The name of the VNF instance to be provisioned (lower-case). | "vm-series-fw-vsi" |
| `ssh_key_name` | The name of your public SSH key to be used for VSI. Follow [Public SSH Key Doc](https://cloud.ibm.com/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-ssh-keys) for creating and managing ssh key. | "vm-series-ssh-key" |


## Notes

If there is any failure during VSI creation, the created resources must be destroyed before attempting to instantiate again. 
- If you are using IBM Cloud Schematics: 
    - To destroy resources go to `Schematics -> Workspaces -> [Your Workspace] -> Actions -> Delete` to delete  all associated resources.
- If you are using Terraform CLI:
    - Execute `terraform destroy --auto-approve` 

# Post VM-Series VSI Instance Spin-up

1. From the VPC list, confirm the VM-Series VSI is powered ON with green button
2. Assign a Floating IP to the VM-Series VSI. Refer the steps below to associate floating IP
    - Go to `VPC Infrastructure Gen 2` from IBM Cloud
    - Click `Floating IPs` from the left pane
    - Click `Reserve floating IP` -> Click `Reserve IP`
    - There will be a (new) Floating IP address with status `Unbind`
    - Click Three Dot Button corresponding to the Unbound IP address -> Click `Bind`
    - Select VM-Series instance (eth0) from `Instance to bind` column.
    - After clicking `Bind`, you can see the IP address assigned to your VM-Series-VSI Instance.
3. Wait for VM-Series VSI to boot up.
4. Connect via web browser GUI, https://floatingIPaddress or from the CLI, run `ssh -i private_key.pem admin@<Floating IP>`.

Note: Default credentials are "admin":"admin" when using the VM-Series image. After the first login, you will be prompted to change your password for security reasons. PLEASE CHANGE DEFAULT LOGIN CREDENTIALS

## Support Policy
The code and script in the repo are released under an as-is, best effort, support policy. These scripts should be seen as community supported and Palo Alto Networks will contribute our expertise as and when possible. We do not provide technical support or help in using or troubleshooting the components of the project through our normal support options such as Palo Alto Networks support teams, or ASC (Authorized Support Centers) partners and backline support options. The underlying product used (the VM-Series firewall) by the scripts are still supported, but the support is only for the product functionality and not for help in deploying or using the script itself.
Unless explicitly tagged, all projects or work posted in our GitHub repository (at https://github.com/PaloAltoNetworks) or sites other than our official Downloads page on https://support.paloaltonetworks.com are provided under the best effort policy.
Launch the VM-Series Firewall Using a Terraform Template
Previous
Next
After modifying the templates for your OCI environment, you can launch the VM-Series firewall.
The VM-Series firewall image boots up with the default username and password (admin/admin). To ensure that your VM-Series firewall instance is protected until you can change the default password, restrict the security list of the management subnet to your source IP address before deploying the VM-Series firewall.

    If you have not done so already, install Terraform on your computer.
    In the command line on your computer, access the folder containing your Terraform Template files.
    Initialize and verify the provider; OCI in this case. Execute the following command:

    terraform init

    You see the following upon successful initialization:
    Validate the template files. Execute the following command to validate the files. If this command returns an error, correct the listed error in your files.

    terraform validate

    (optional
    ) You can use the following command to display a plan of your deployment using the data from the templates.

    terraform plan

    Launch the VM-Series firewall instance. Enter yes when prompted.

    terrafom apply

    When the process is complete, the CLI displays the following:
    Verify that your VM-Series firewall instance was launched.
        Log in to the OCI console.
        Select Compute
        Instances
        .
        Select Created Date (Desc)
        from the Sort By
        drop-down to see the most recently created instances.
        Your new VM-Series firewall instance should be listed first.
    Delete default security list rules to prevent access to the firewall until you have changed the default password.
        Select Networking
        Virtual Cloud Networks
        <your VCN>
        Security Lists
        Default Security List
        Edit All Rules
        .
        Click the delete icon to delete each rule.
        After deleting each rule, click Save Security List Rules
        .
    Change the default password through a console connection to the firewall.
        Configure a console connection.
            Generate a public key and copy it.
            Log in to the OCI console.
            Select Compute
            Instances
            and click your VM-Series firewall instance.
            Select Console Connections
            Create Console Connection
            Paste SSH Keys
            .
            Paste your key and click Create Console Connection
            .
        Open a console connection to the VM-Series firewall.
            Select Connect with SSH
            .
            Copy the string used to open the console connection to the VM-Series firewall.
            Open a terminal on your computer and, from the directory containing your keypair, paste the string you copied above and hit Enter.
        Change the password.
            Enter config mode.

            admin@PA-VM> configure

            Execute the following command to change the password. When prompted, enter your new password and enter it again to confirm.

            admin@PA-VM# set mgt-config users admin password

            Commit your changes.
    Add route table rules to the management route table to give yourself SSH and web interface access to the firewall.
        Select Networking
        Virtual Cloud Networks
        and click your VCN.
        Select Route Tables
        and click your management route table.
        Select Edit Route Rules
        + Another Route Rule
        .
        Select your compartment.
        Enter a descriptive Name
        for your route table.
        Select a target type. For subnets that are publicly accessible, select Internet Gateway.
        Enter a Destination CIDR Block
        .
        Select the internet gateway you created previously from the Target Internet Gateway
        drop-down.
        Click Create Save
        .
    Edit security lists to give yourself SSH and web interface access to the firewall.
        From your VCN, select Security Lists
        mgmt-security-list
        Edit All Rules
        .
        mgmt-security-list is the default name used in the Terraform Template file. If you have changed this value, locate you management security list.
        Select CIDR from the Source Type
        drop-down and Source CIDR
        block.
        Select TCP from the IP Protocol
        drop-down.
        Enter source and destination ports or port ranges. If you leave these fields blank, all ports are allowed. Port 22 is required for SSH access and port 443 is required for SSL access to the firewall web interface.
        Click Create Security List
        .
    Configure the dataplane network interfaces as Layer 3 interfaces on the firewall.
        Log in to the firewall.
        Select Network
        Interfaces
        Ethernet
        .
        Click the link for ethernet 1/1
        and configure as follows:
            Interface Type
            : Layer3
            On the Config
            tab, assign the interface to the default router.
            On the Config
            tab, expand the Security Zone
            drop-down and select New Zone
            . Define a new zone, for example untrust-zone, and then click OK
            .
            On the IPv4
            tab, select either Static
            or DHCP Client
            .
            If using the Static
            option, click Add
            in the IP section, and enter the IP address and network mask for the interface. Make sure that the IP address matches the IP address that you assigned to the corresponding subnet in VCN. For example, if you add this interface to your untrust zone, make sure you assign the untrust vNIC IP address configured in your VCN.
        Repeat this procedure for each vNIC configured in your VCN except your management vNIC.
    Create NAT rules to allow inbound and outbound traffic form the servers deployed in VCN.
        Select Policies
        NAT
        on the web interface of the firewall.
        Create a NAT rule that allows traffic from the external-facing or untrust dataplane network interface on the firewall to the trust interface in the VCN.
        Create a NAT rule that allows outbound access for traffic from inside the VCN to the internet.
    Disable DPDK on the firewall. The VM-Series firewall on OCI supports Packet MMAP only. DPDK is enabled by default and must be disabled.
        Log in to the firewall CLI.
        Disable DPDK.
        admin@PA-VM> set system setting dpdk-pkt-io off
        Restart the firewall.
    (optional
    ) If you need more storage than the minimum 60GB required by the VM-Series firewall, you can create and attach a storage volume to your VM-Series firewall instance.
        Log in to the OCI console.
        Select Storage
        Block Volumes
        Create Block Volume
        .
        Select your compartment.
        Enter a descriptive Name
        for your block storage volume.
        Select an Availability Zone
        .
        Enter the size for your block volume.
        Click Create Block Volume
        .
        Select Compute
        Instances
        , click on your newly-created instance, and select Attached Block Volumes
        Attach Block Volume
        .
        Select Paravirtualized
        .
        Select your compartment.
        Select the block volume you created previously from the Block Volume drop-down.
        Select Read/Write
        .
        Click Attach
        .
        Reboot the VM-Series firewall instance by clicking Reboot
        on the Instance Details page.

