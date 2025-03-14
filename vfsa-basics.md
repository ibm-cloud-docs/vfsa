---

copyright:
  years: 2025
lastupdated: "2025-03-14"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Performing other vFSA tasks
{: #performing-ibm-cloud-fortinet-vfsa-basics}

You can configure and maintain your {{site.data.keyword.vfsa_full}} (vFSA) in various ways, including using SSH to connect directly to the vFSA VM, logging in to the vFSA web management GUI, or by using SSH to connect to the Ubuntu hypervisor and then initiating a KVM console session to the vFSA VM. 
{: shortdesc}

You can also configure and manage multiple FortiGates at the same time using the [FortiManager](https://www.fortinet.com/products/management/fortimanager) appliance (virtual or hardware) or with other automation and orchestration tools, such as Ansible, that use the Fortinet API.

By connecting your {{site.data.keyword.vfsa_full}} to a [FortiAnalyzer](https://www.fortinet.com/products/management/fortianalyzer) appliance (virtual or hardware), you can receive analytics, centralized logging, automation, and visibility into all of your appliances and devices within the Fortinet Security Fabric.

## Accessing the device using SSH
{: #accessing-the-device-using-ssh}

By default the public interfaces of the vFSA are disabled. However, private interfaces allow connectivity to both the host and the VM. For more information, refer to [Accessing your FortiGate Virtual Security Appliance](/docs/vfsa?topic=vfsa-vfsa-accessing).

Once private network connectivity is setup:

1. Go to the Gateway Appliance Details page and retrieve the Private gateway IP.

1. Click the "eye" icon to reveal the admin user's password.

1. For a vFSA, in an SSH client or terminal, run the command `ssh admin@<gateway-ip>`, then enter the admin user's password.

   For the host (Ubuntu), use the `root` user ID and password. SSH to the Ubuntu host is only enabled on the private interface.
   {: note}

If you do not see the "eye" icon, you might not have permission to view the password. Check your access permissions with the account owner.
{: note}

## vFSA command-line basics
{: #command-line-basics}

The vFSA has a suite of CLI commands to configure firewall policies, routes, and all of the features that come with the product. More details can be found [here](https://docs.fortinet.com/document/FortiGate/7.4.1/administration-guide/896276/cli-basics){: external}.

To see the current build running on the vFSA:

```sh
get system status
```

To get the current status of the HA cluster (if not standalone):

```sh
get system ha status
```

To show the current firewall policy:

```sh
show firewall policy
```

## Accessing the device using the vFSA web management UI
{: #accessing-the-device-using-the-vfsa-web-management-ui}

The vFSA web management GUI has been configured, by default, with a vFSA generated self-signed certificate. Only HTTPS is enabled on port 443. You can access it at `https://<vFSA Private IP>:443`. It is also accessible directly from the vFSA Gateway Details page by clicking **vFSA > Public IP** if the Public interface is enabled for the web management console (by default it is not). Keep both the SSH and HTTPS management locked down to the private IP, as both ports are widely targeted on public networks for scans and attacks. HTTP and HTTPS vulnerabilities are constantly being found. This advice pertains to any device you have access to.
