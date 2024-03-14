---

copyright:
  years: 2024
lastupdated: "2024-03-05"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

keywords: basics, performing, accessing, ssh, device, gateway, configuration, mode, juniper, ui, dns, htp, password

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Performing additional vFSA tasks
{: #performing-ibm-cloud-juniper-vfsa-basics}

You can configure and maintain your {{site.data.keyword.vfsa_full}} in a variety of ways, either through a remote console session through SSH or by logging into the vFSA web management GUI.
{: shortdesc}

Configuring the vFSA outside of its shell and interface might produce unexpected results and is not recommended.
{: note}

## Accessing the device using SSH
{: #accessing-the-device-using-ssh}

By default the public interfaces of the vFSA are disabled. However, private interfaces allow connectivity to both the host and the VM. For more information refer to [Accessing your FortiGate Virtual Security Appliance](/docs/vfsa?topic=vfsa-vfsa-accessing).

Once private network connectivity is setup:

1. Go to Gateway Appliance Details page and retrieve the Private gateway IP.

2. Click the "eye" icon to reveal the admin user's password.

3. For a vFSA, run the command `ssh admin@<gateway-ip>`, then enter the admin user's password.

   For the host (Ubuntu), use the `root` user ID and password. SSH to the Ubuntu host is only enabled on the private interface.
   {: note}

If you do not see the "eye" icon, you might not have permission to view the password. Please check your access permissions with the account owner.
{: note}

## vFSA command line basics
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

The vFSA web management GUI has been configured by default, with a vFSA generated self-signed certificate. Only HTTPS is enabled on port 443. You can access it at `https://<vFSA Private IP>:443`. It is also accessible directly from the vFSA Gateway Details page through the clickable link under **vFSA > Public IP** if the Public interface is enabled for the web management console (by default it is not enabled).
