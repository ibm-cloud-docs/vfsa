---

copyright:
  years: 2018, 2023
lastupdated: "2023-12-20"

keywords: secure, securing, host, os, ubuntu, ufw, iptables, firewall, juniper

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Securing the host operating system
{: #securing-host-operating-system}
{: help}
{: support}

The {{site.data.keyword.vfsa_full}} runs as a virtual machine on a bare-metal server that is installed with Ubuntu and KVM. To secure the host OS, ensure that no other critical services are hosted on the same OS.
{: shortdesc}

## SSH access
{: #securing-host-ssh}

The {{site.data.keyword.vfsa_full}} is always deployed with public and private network access. By default, password-based SSH access to the public IP of the host OS is disabled on new provisions and OS reloads, and any public interfaces will not be listening for SSH. Access to the host can be achieved through the private IP address. Alternatively, you can use key-based authentication to access the public IP. To do so, specify the public SSH key when placing a new gateway order.
   
## Firewalls
{: #securing-host-firewall}

If you are implementing an Ubuntu firewall (UFW, Iptables, and so on) ensure that you do not block root SSH access to the private interface if you are planning to run the following actions: 

- OS reload and license upgrade readiness checks
- OS reloads
- Cluster rebuilds
- License upgrades

Most gateway actions require SSH access to the private `10.0.0.0/8` subnet for the host OS and the {{site.data.keyword.vfsa_full}}. 

As a result, if SSH access is disabled for the `10.0.0.0/8` subnet, you must re-enable it before running any of these actions.
