# Creating a Tenant

Open the VKP home page and click the "Create" button.

Enter the name of your tenancy:

!!! info
	The tenancy must follow the standard Kubernetes [resource naming restrictions](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

![Tenant creation wizard](tenant-wizard.png)

Once created, the tenancy will enter the `PendingApproval` state.

![List of tenants with one awaiting approval](tenant-pending.png)

You will need to wait for a VKP administrator to approve the tenancy.

![Status messages shown on an unapproved tenancy](tenant-pending2.png)

Once approved, you will be able to create clusters inside this tenancy.
