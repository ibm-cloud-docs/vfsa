---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: IAM access for vFSA, permissions for vFSA, identity and access management for vFSA, roles for vFSA, actions for vFSA, assigning access for vFSA

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Managing IAM access for IBM Cloud Virtual FortiGate Security Appliance
{: #iam-docs-template}

Access to {{site.data.keyword.vfsa_full}} service instances for users in your account is controlled by {{site.data.keyword.cloud}} Identity and Access Management (IAM). Every user that accesses the vFSA service in your account must be assigned an access policy with an IAM role. Review the following roles, actions, and more to help determine the best way to assign access to vFSA.
{: shortdesc}

The access policy that you assign users in your account determines what actions a user can perform within the context of the service or specific instance that you select. The allowable actions are customized and defined by the vFSA as operations that are allowed to be performed on the service. Each actions is mapped to an IAM platform or service role that you can assign to a user.

If a specific role and its actions don't fit the use case that you're looking to address, you can [create a custom role](/docs/account?topic=account-custom-roles#custom-access-roles) and pick the actions to include.
{: tip}

IAM access policies enable access to be granted at different levels. Some of the options include the following:

* Access across all instances of the service in your account
* Access to an individual service instance in your account
* Access to a specific resource within an instance, _such as resource type `bucket`_

Review the following tables that outline what types of tasks each role allows for when you're working with the vFSA service.

When you assign policies to users for this service, use `servicename` for the service name in the CLI command or API call.
{: important}

The following table describes the types of tasks that can be completed with each platform management role assigned. Platform management roles enable users to perform tasks on service resources at the platform level, for example, assign user access to the service, create or delete instances, and bind instances to applications.

| Platform role | Description of actions |
|--------------------------|------------------------|
| Viewer                   | Description of what users can accomplish; think tasks.        |
| Operator                 | Description            |
| Editor                   | Description            |
| Administrator            | Description            |
{: row-headers}
{: caption="Table 1. IAM platform roles" caption-side="bottom"}
{: summary="The rows are read from left to right. The first column is the platform management role. The second column is a description of the actions in the service that the platform management role permits."}

The following table describes the types tasks that can be completed when each service access role is assigned. Service access roles enable users access to vFSA and the ability to call the `service name's` API.

| Service role | Description of actions |
|---------------------|------------------------|
| Reader              | Description of what users can accomplish; think tasks.   |
| Writer              | Description            |
| Manager             | Description            |
{: row-headers}
{: caption="Table 2. IAM service access role descriptions" caption-side="bottom"}
{: summary="The rows are read from left to right. The first column is the service access role. The second column is a description of the actions in the service that the service access role permits."}

For information about the steps to assign IAM access, see [Managing access to resources](/docs/account?topic=account-assign-access-resources).
