---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

keywords: confirming, output, committing, changes, vlans, ssh

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Confirming the output and committing the changes
{: #confirming-the-output-and-commiting-the-changes}

After the changes and additions you've made have been staged, run the following command to confirm what will be committed to the active configuration:
{: shortdesc}

```sh
show | compare
```

This is the expected output:

```text
[edit security address-book global]
     address SL_PUB_MGMT { ... }
+    address VSI_PUB_NET 169.47.211.152/29;
[edit security policies]
     from-zone SL-PUBLIC to-zone SL-PUBLIC { ... }
+    from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC {
+        policy ALLOW_INTERNAL {
+            description "Allow all traffic within CUSTOMER_PUBLIC zone";
+            match {
+                source-address any;
+                destination-address any;
+                application any;
+            }
+            then {
+                permit;
+            }
+        }
+    }
+    from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC {
+        policy ALLOW_OUTBOUND {
+            description "Allow all outbound traffic from CUSTOMER-PUBLIC to the internet";
+            match {
+                source-address any;
+                destination-address any;
+                application any;
+            }
+            then {
+                permit;
+            }
+        }
+    }
+    from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC {
+        policy ALLOW_PING_SSH {
+            description "Allow ping and SSH to public subnet";
+            match {
+                source-address any;
+                destination-address VSI_PUB_NET;
+                application [ junos-ssh junos-ping ];
+            }
+            then {
+                permit;
+            }
+        }
+    }
[edit security zones]
     security-zone SL-PUBLIC { ... }
+    security-zone CUSTOMER-PUBLIC {
+        interfaces {
+            agg3.1523 {
+                host-inbound-traffic {
+                    system-services {
+                        all;
+                    }
+                }
+            }
+        }
+    }
[edit interfaces agg3]
+    unit 1523 {
+        vlan-id 1523;
+        family inet {
+            address 169.47.211.153/29;
+        }
+    }
```
{: screen}

After you check that the configuration is correct, run the `commit` command to push the changes to the active configuration.

Your {{site.data.keyword.vfsa_full}} Standard is now configured to route and filter traffic to the new VLAN and subnet, allowing only inbound pings and SSH connections.

You should now route the VLAN as shown in [Manage VLANs](/docs/gateway-appliance?topic=gateway-appliance-managing-vlans-and-gateway-appliances) to begin using the new functionality.
