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

## Service URL

- http://prometheus.xjulio.me:9090
- http://grafana.xjulio.me:3000
