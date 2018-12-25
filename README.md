# ansible-prometheus-server

The project aim to demonstrate how to use ansible to create automatically and configure a virtual machine (VM) in Google Cloud Platform (GCP) with Prometheus Server and Grafana.

Future udpates will include support to another cloud providers.

## Features

- Playbook to create a VM on GCP with fixed IP reservation, definition of Firewall Rules to allow only traffic to Prometheus/Grafana, create ssh key pair and inject into VM instance to ansible management. 
   * https://github.com/xjulio/ansible-prometheus-server/blob/master/playbooks/create_prometheus_vm.yml
- Playbook to install prometheus server into CentOS 7 VM, configuring users, groups, systemd service and initial prometheus configuration. Also the metrics scrapping from SpringBoot application server are inject in prometheus.yml configuration using file_sd_configs to load targets from a json file that contains the FQDN of jetty server.
   * https://github.com/xjulio/ansible-prometheus-server/blob/master/playbooks/install_prometheus.yml
- Playbook to install and configure grafana server into CentOS VM.
   * https://github.com/xjulio/ansible-prometheus-server/blob/master/playbooks/install_grafana.yml

## Prerequirement installation

- Ansible 2.7
- Python 2.7
- Google Cloud SDK
- Docker Community Edition 18.09

To install Google Cloud SDK follow the steps in https://cloud.google.com/sdk/docs/quickstarts. The MacOS guide isL

```
wget https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-218.0.0-darwin-x86_64.tar.gz
tar zxvf google-cloud-sdk-218.0.0-darwin-x86_64.tar.gz
./google-cloud-sdk/install.sh
```

To initialize the SDK run the following at a command prompt:

```
gcloud init
```

Accept the option to log in using your Google user account and choose your project and default region in GCP.

Install Kubernetes command-line tool (kubectl):

```
gcloud components install kubectl
```

Set defaults for the gcloud command-line tool:

```
gcloud config set project [PROJECT_ID]
gcloud config set compute/zone us-central1-b
```

## Usage

Clone the repository:

```
git clone https://github.com/xjulio/ansible-prometheus-server.git
```

### Configuring variables

Adjust the variables in ansible-prometheus-server/vars/main.yml file:
```
# default ssh key created by playbook. 
ssh_key_content: "{{ lookup('file', '../keys/ansible_rsa.pub') }}"

# adittional ssh key to be inject in prometheus VM. MUST BE CHANGED
ssh_xjulio_key_content: "{{ lookup('file', '/Users/xjulio/.ssh/id_rsa.pub') }}"

# Google Cloud Project. MUST BE CHANGED
gcp_project: mb-demo-224014

# Google Cloud credential type
gcp_cred_kind: serviceaccount

# Google Cloud credentials file. MUST BE CHANGED
gcp_cred_file: /Users/xjulio/gcloud-cred-ansible.json

# Google Cloud service email. MUST BE CHANGED
gcp_service_email: "ansible@mb-demo-224014.iam.gserviceaccount.com"

# Default Zone
gcp_zone: "us-central1-a"

# Default region
gcp_region: "us-central1"
```

In file ansible-prometheus-server/vars/main.yml adjust the address of prometheus target address and port with prometheus metrics exposed under /prometheus uri:

```
jetty_master_addr: mbird.demo.xjulio.me
jetty_master_port: 80 
```

### Creating the VM on GCP
Execute the ansible playbook, enter inside the directory:

```
cd ansible-prometheus-server/playbooks
ansible-playbook create_prometheus_vm.yml
```
This playbbok will create an external fixed elastic IP that will be display during execution. Get this IP and update your /etc/ansible/hosts file with:

```
[prometheus]
35.188.179.229
```

If this entry not be present in /etc/ansible/hosts, the execution of another playbooks will fail.

###  Installing Prometheus Server
Execute the ansible playbook, enter inside the directory:

```
cd ansible-prometheus-server/playbooks
ansible-playbook install_prometheus.yml
```

This playbbok will perform all operations to prepare the VM to prometheus server to be operational: create users/groups, systemctl service creating, prometheus configuration and service initialization.

After the playbbok execution end, the Prometheus Server will be accessible on Internet using the IP address displayed previoysly on port 9090. 

- http://35.188.179.229:9090 (The ip used in URL will be a new IP created by previous playbook, this is an example)

###  Installing Grafana Server
Execute the ansible playbook, enter inside the directory:

```
cd ansible-prometheus-server/playbooks
ansible-playbook install_grafana.yml
```

This playbbok will perform all operations to prepare the VM to grafanaserver to be operational: create users/groups, systemctl service creating and service initialization.

After the playbbok execution end, the Grafana Server will be accessible on Internet using the IP address displayed previoysly on port 3000. 

- http://35.188.179.229:3000 (The ip used in URL will be a new IP created by previous playbook, this is an example). The default user and password is admin.
