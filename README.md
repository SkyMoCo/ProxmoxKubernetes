# Bare Metal Kubernetes 


## Summary
Declaratively build a maintain a multi node Kubernetes cluster on Proxmox using Ansible 

Add playbooks for uding ingress, load balancing, unattended upgrades, the Dashboard, etc.

**Approximate deployment time:** 25 minutes


## Requirements
1. Proxmox server
2. DNS Server
3. DHCP Server


## Instructions
**Required:**

1. Modify the `vars.yml` file with values specific to your environment.
2. Create DNS A records for the IP Addresses & Hostnames you defined for your nodes in the `vars.yml` file.
3. Create DHCP records for the MAC addresses.   I just generated my own using my given host names
   macaddr=$(echo $FQDN|md5sum|sed 's/^\(..\)\(..\)\(..\)\(..\)\(..\).*$/02:\1:\2:\3:\4:\5/')
3. Modify the `inventory.ini` file to reflect your chosen DNS records and the location of the SSH keys used to connect to the nodes.
4. Run the deployment: `ansible-playbook -i inventory.ini site.yml`
5. After deployment, a `~/.kube` directory will be created on your workstation. You can use the `config` file within to interact with your cluster.

**Optional:**

*To enable an optional feature, fill in the additional parameters in `vars.yml` and execute a playbook listed below.*

| Feature | Command | Requirements |
| ------- | ------- | ------------ |
| [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) | `ansible-playbook -i inventory.ini playbooks/optional/deploy_dashboard.yml` | |
| [NFS backed persistent storage](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client) | `ansible-playbook -i inventory.ini playbooks/optional/deploy_nfs_provisioner.yml` | |
| [MetalLB Load Balancer](https://metallb.universe.tf) | `ansible-playbook -i inventory.ini playbooks/optional/deploy_metallb.yml` | | 
| [NGINX Ingress Controller](https://github.com/kubernetes/ingress-nginx) | `ansible-playbook -i inventory.ini playbooks/optional/deploy_ingress-nginx.yml` | [MetalLB](https://metallb.universe.tf/) or other Load Balancer integration |
| [DataDog agents](https://docs.datadoghq.com/integrations/kubernetes/) | `ansible-playbook -i inventory.ini playbooks/optional/deploy_datadog.yml` | |
| [Unattended Upgrades](https://wiki.debian.org/UnattendedUpgrades) | `ansible-playbook -i inventory.ini playbooks/optional/enable_unattended_upgrades.yml` | |


## Tips
1. You can rollback the entire deployment with: `ansible-playbook -i inventory.ini playbooks/optional/delete_all_resources.yml`


ToDo
1. Explain things a little better
2. Add playbook to add another node to a cluster
3. Try using different qcow2 images as the debian one, while slow, has issues.
4. All of this, including the DNS and MAC addresses could be in ansible....  