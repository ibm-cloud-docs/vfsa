---

copyright:
  years: 2024
lastupdated: "2024-02-06"

keywords: checking, readiness, errors

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Understanding readiness errors and warnings
{: #readiness-errors}

Readiness check warnings and errors can occur for any of the three readiness checks: OS reload, license upgrade, or version upgrade. The following list provides information on these error and warning codes.

## Error 1000
{: #error-1000}

An OS reload is already running on a different node in the HA cluster. The other node's OS reload must complete before executing a second OS reload.

## Error 1002
{: #error-1002}

An error occurred in the connectivity check. Contact IBM Support.

## Error 1003
{: #error-1003}

The root user for the host is not found.

## Error 1004
{: #error-1004}

The root user credentials for the host are not found.

## Error 1005
{: #error-1005}

The host SSH console connection could not be created. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1007
{: #error-1007}

The host SSH console connection could not be created. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1008
{: #error-1008}

The host SSH console could not process the command. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1009
{: #error-1009}

The host has an invalid IP address, or is blocked by a firewall. SSH failed. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1011
{: #error-1011}

The root user's credentials for the gateway were not found.

## Error 1012
{: #error-1012}

The gateway SSH console connection failed. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1013
{: #error-1013}

The root user for the gateway was not found.

## Error 1014
{: #error-1014}

The gateway readiness check has timed out.

## Error 1017
{: #error-1017}

The gateway SSH console connection could not be created. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1018
{: #error-1018}

The gateway SSH console could not process the command. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1019
{: #error-1019}

The gateway has an invalid IP address, or is blocked by a firewall. SSH failed. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1021
{: #error-1021}

An error occurred in the connectivity check. Contact IBM Support.

## Error 1022
{: #error-1022}

The gateway readiness precheck failed with an unexpected exception. Try again or contact IBM Support.

## Error 1023
{: #error-1023}

The gateway readiness precheck failed the DNS accessibility check on the Ubuntu host. Confirm there is network access to the private `10.0.0.0/8` subnet from the host.

## Error 1024
{: #error-1024}

The gateway readiness precheck failed with an unexpected exception. Try again or contact IBM Support.

## Error 1025
{: #error-1025}

The IPMI interface is disabled for the gateway host. Check IPMI status and enable the interface if is disabled. Try again or contact IBM Support.

## Error 1026
{: #error-1026}

The Ubuntu host is unable to write to the attached storage as it is read only. Reboot the host and try again or contact IBM Support.

## Error 1101
{: #error-1101}

An error occurred during the readiness check. Try again or contact IBM Support.

## Error 1104
{: #error-1104}

The host experienced a precheck setup failure. Try again or contact IBM Support.

## Error 1105
{: #error-1105}

The host experienced a precheck setup failure and cannot SSH to the gateway. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1107
{: #error-1107}

The host experienced a precheck setup failure due to an invalid password. Correct the password and try again. Refer to [Correcting connectivity errors](/docs/vfsa?topic=vfsa-correcting-readiness-errors) for information on how to resolve SSH connectivity check errors.

## Error 1114
{: #error-1114}

The host has a precheck setup failure because insufficient memory is installed on the bare-metal server. A minimum of 32 GB is required. Contact IBM Support to install the required amount of memory.

## Error 1117
{: #error-1117}

The host's Ubuntu mirror server is not accessible. Retry the operation once the mirror is accessible.

## Error 1118
{: #error-1118}

The vFSA node is not started. Start the vFSA VM and retry the operation.

## Error 1119
{: #error-1119}

The vFSA node's VM is running but the node is not accessible via telnet. Exit from all console (telnet) sessions and retry the operation.

## Error 1120
{: #error-1120}

The vFSA node does not have a valid license. Remove any evaluation licenses and apply a valid license. Then retry the operation. 

## Error 1127
{: #error-1127}

The Ubuntu host is missing an ethernet interface in bond0. Ensure bond0 includes the 2 private ethernet interfaces.

## Error 1128
{: #error-1128}

The Ubuntu host is missing an ethernet interface in bond1. Ensure bond1 includes the 2 public ethernet interfaces.

## Error 1129
{: #error-1129}

The Ubuntu host has an extra ethernet interface in bond0. Ensure bond0 only includes the 2 private ethernet interfaces.

## Error 1130
{: #error-1130}

The Ubuntu host has an extra ethernet interface in bond1. Ensure bond1 only includes the 2 public ethernet interfaces.

## Error 1148
{: #error-1148}

The vFSA HA cluster is not formed. The status is not in-sync. Contact IBM Support.

## Error 1149
{: #error-1149}

Unable to detect a valid vFSA license on the second node in the cluster.	Try again or contact IBM Support.

## Error 1150
{: #error-1150}

A private only vFSA was specified. Only a public/private vFSA network configuration is supported.

## Error 1151
{: #error-1151}

No network connectivity to the public port of the vFSA.	Enable ping on the vFSA public IP and retry.

## Error 1152
{: #error-1152}

Unable to detect the vFSA build version.	Try again or contact IBM Support.

## Error 1153
{: #error-1153}

The detected vFSA build version is not supported by the IBM vFSA automation. Use the native vFSA upgrade/downgrade tool to load a supported version and retry. 

## Error 1155
{: #error-1155}

The vFSA private IP is not pingable.	Ensure the private IP allows icmp and try again.

## Error 1156
{: #error-1156}

Unable to retrieve vFSA information. Try again or contact IBM Support.

## Error 1164
{: #error-1164}

The vFSA HA Cluster nodes are not in-sync. Contact IBM Support.
