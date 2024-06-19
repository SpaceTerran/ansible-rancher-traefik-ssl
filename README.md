This repository contains Ansible playbooks and roles for setting up and installing Helm, Cert-Manager, Traefik, and Rancher on RKE2 clusters, as seen in my blog post here: [Automate Your RKE2 Cluster with Ansible: Helm, Cert-Manager, Traefik, and Rancher Setup Made Easy](https://spaceterran.com/posts/Automate-Your-RKE2-Cluster-with-Ansible-Helm-Cert-Manager-Traefik-and-Rancher-Setup-Made-Easy/)

## Repository Structure

```plaintext
├── inventory/
│   ├── hosts.ini
│   └── group_vars/
│       └── all.yml
├── playbook.yml
├── requirements.yml
├── secrets.yaml
├── roles/
│   ├── common/
│   │   └── tasks/
│   │       └── main.yml
│   ├── helm/
│   │   ├── tasks/
│   │       └── main.yml
│   ├── helm_plugins/
│   │   └── tasks/
│   │       └── main.yml
│   ├── cert_manager/
│   │   ├── tasks/
│   │       └── main.yml
│   │   └── templates/
│   │       ├── cloudflare-api-key-secret.yaml.j2
│   │       └── clusterissuer-letsencrypt-cloudflare.yaml.j2
│   ├── traefik/
│   │   ├── tasks/
│   │       └── main.yml
│   │   └── templates/
│   │       ├── values.yaml.j2
│   │       └── certificate-wildcard-spaceterran-traefik.yaml.j2
│   ├── rancher/
│   │   ├── tasks/
│   │       └── main.yml
│   │   └── templates/
│   │       ├── values.yaml.j2
│   │       └── certificate-wildcard-spaceterran-rancher.yaml.j2
```

### Install Required Ansible Collections

Before running the playbooks, you need to install the required Ansible collections. You can do this by running:

```bash
ansible-galaxy collection install -r requirements.yml
```

## Usage

### Inventory Configuration

Update your `hosts.ini` file with the target servers and any required variables:

**inventory/hosts.ini:**

```ini
[servers]
server1 ansible_host=192.168.2.126
server2 ansible_host=192.168.2.125
server3 ansible_host=192.168.2.124

[servers:vars]
ansible_user=adminuser
ansible_become=true

[agents]
agent1 ansible_host=192.168.2.123
agent2 ansible_host=192.168.2.122

[agents:vars]
ansible_user=adminuser
ansible_become=true

```

### Variable Configuration

Update the centralized variables in `inventory/group_vars/all.yml`:

**inventory/group_vars/all.yml:**

```yaml
---
# Home base path
home_path: /home/adminuser

kubectl_config: "{{ home_path }}/.kube/config"

# https://github.com/helm/helm/releases
helm_version: v3.15.1

cert_manager_chart_ref: jetstack/cert-manager
# https://github.com/cert-manager/cert-manager/releases
cert_manager_chart_version: v1.15.0
cert_manager_path: "{{ home_path }}/cert-manager"
cert_manager_email: your_email@your_domain.com

traefik_chart_ref: traefik/traefik
# https://github.com/traefik/traefik-helm-chart/releases
traefik_chart_version: 28.3.0
traefik_path: "{{ home_path }}/traefik"

# https://github.com/rancher/rancher/releases
rancher_chart_ref: rancher-alpha/rancher
rancher_chart_version: 2.9.0-alpha5
rancher_path: "{{ home_path }}/rancher"
```

### Running the Playbook

To execute the playbook, run:

```bash
ansible-playbook -i inventory/hosts.ini playbook.yml --ask-vault-pass
```