---
title: Manage your Dynamics 365 Customer Insights - Journeys environments
description: How to copy a production Dynamics 365 Customer Insights - Journeys environment to a sandbox environment for experiments and testing.
ms.date: 08/21/2023
ms.topic: article
author: alfergus
ms.author: alfergus
search.audienceType: 
  - admin
  - customizer
  - enduser
---

# Manage your Dynamics 365 Customer Insights - Journeys environments

[!INCLUDE[consolidated-sku-rtm-only](./includes/consolidated-sku-rtm-only.md)]

Environment-management operations are a standard feature of model-driven apps in Dynamics 365 (Dynamics 365 Sales, Dynamics 365 Customer Service, Dynamics 365 Field Service, Customer Insights - Journeys, and Dynamics 365 Project Service Automation). Customer Insights - Journeys, however, adds significant complexity to the system, so there are several extra considerations to keep in mind when you have Customer Insights - Journeys installed.

> [!IMPORTANT]
> This topic provides details about the exceptions that apply when working with environments where the Customer Insights - Journeys app is installed. For all other management tasks, see [Environments overview](/power-platform/admin/environments-overview), but read this topic first.

> [!NOTE]
> *Environments* are sometimes also known as *organizations* or *environments*. Each of these terms refers to the same concept. The Power Apps user interface and documentation usually use the term *environment*, while Customer Insights - Journeys documentation usually uses the term *environment* (though you'll sometimes also see the terms *organization* or *org* here). Because this article relies on Power Platform Admin Center functionality, it refers exclusively to environments.

> [!TIP]
> If you have not installed other apps on the [Microsoft Power Platform admin center](/power-platform/admin/), you will need to create an environment before you can run the Customer Insights - Journeys setup wizard. Learn more: [Create and manage environments in the Power Platform admin center](/power-platform/admin/create-environment).

## Elements in a Customer Insights - Journeys environment

Customer Insights - Journeys is composed of several components that you must be aware of to understand how the environment-management operations work when Customer Insights - Journeys is installed. These include the following elements:

- **Dynamics 365 environment**. This provides the basic platform for the Customer Insights - Journeys app and includes both a _platform server_ and an _organizational database_. This database is also shared by the Customer Insights - Journeys app, which reads and stores much of its data here.
- **Customer Insights - Journeys application**. This is a collection of solutions that are installed on the platform server and add marketing features to Dynamics 365. It's installed on the platform server to add marketing features to Dynamics 365. It's also referred to as the _Marketing app_.
- **Customer Insights - Journeys services**. This is a collection of services that the Customer Insights - Journeys app interacts with. They run in parallel with your Dynamics 365 environment. Among other things, live customer journeys and marketing email messages run on marketing services. Also, all images uploaded to Customer Insights - Journeys for use in emails and marketing pages are stored and served from here.
- **Customer Insights - Journeys-insights service**. This provides big data services such as resolving segment queries, collecting and storing interaction data, and providing analytics based on this data. The marketing-insights service is just one of the marketing services already mentioned, but it's worth calling out separately because it comes up often.

> [!IMPORTANT]
> Customer Insights - Journeys services (including the marketing-insights service) run in parallel with your Customer Insights - Journeys environment, and thus follow their own lifecycle. These services aren't directly accessible to users, and the data they contain isn't included when copying, backing up, or restoring a Customer Insights - Journeys environment. This means that interaction records (such as email clicks and website visits) and files (such as those used in emails and marketing pages) aren't included when you copy, backup, or restore an environment.

<a name="target-content"></a>

## Content of the target environment after a copy or restore

After copying or restoring an environment, as described later in this article, your target environment will be set up as follows:

- All apps, settings, and customizations from your source environment will be present on the target environment.
- For copies, if you chose to do an "Everything" copy, the entire organizational database of your source environment will be copied to the target environment. This means that copied data from your source environment will be visible on the target environment, but your work in the target environment won't affect your source database from now on.
- For copies, if you chose to do a "Customizations and schemas only" copy, all your apps and customizations will still be present on the target environment, but the organizational database will be nearly empty, so none of your source data (including email messages, portal content, and customer journeys) will be there.
- All records (except for customer journeys) that were live on the source environment (such as emails, lead-scoring records, and more) will revert to **Draft** state on the target environment. You must go live again with any of these records that you want to use on the target environment.
  > [!NOTE]
  > For customer journeys:
  > - All Expired/Draft journeys will be left as it is.
  > - All other journeys will be cloned in **Draft** state and the original journeys will be left in place with an **Expired** state.
- After any copy or restore operation, you must [run the setup wizard](setup.md#run-wizard) on the target environment. This will create a new set of Customer Insights - Journeys services (including a new marketing-insights service) and link them to the target environment. If you don't run the wizard, all features that require services (such as insights and email sending) won't work, and you'll still see information about images in the files library for which the source files aren't available.
- Because a new set of Customer Insights - Journeys services is created on the target environment, interaction data from your source environment (such as email clicks or website visits) won't be available to the target environment. Most insights data will be initialized. You can freely generate new interaction data on the target environment without affecting your source environment.
- Files uploaded to your source environment (such as images used in emails and landing pages) won't be available to the target environment. If you go live with an email or page that was previously published on the source environment, the published design will continue to use the previous image URLs from the source environment&mdash;these images will still appear in the republished designs provided they are still available on the source environment, but to avoid confusion, we strongly recommend that you upload all the images you need to the new environment and edit your emails and pages to use those images before going live with them again.
- If the Customer Insights - Journeys app on your source environment used a Power Apps portal, then you might choose to also set up a new portal on the target environment to host its marketing pages and event websites (requires an unconfigured Power Apps portals license to be available on your tenant). [Portals are optional](portal-optional.md), so you can choose not to use a portal with the copied environment if you prefer, even if the source environment was using one.
- After a Customer Insights - Journeys is migrated, restored, or copied, its state is changed from **Live** to **Stopped**. To restart a migrated, restored, or copied journey, you need to first duplicate the journey, and then execute it.
- After backup, if you restore data in Customer Insights - Journeys, all interaction data, analytics data, Customer Voice data, asset library connections, and Customer Insights connections will *not* be restored. All existing data will remain.

<a name="copy-to-sandbox"></a>

## Copy a Customer Insights - Journeys environment to another environment

Because Customer Insights - Journeys is more complex than most Dynamics 365 apps and interacts with several special services and other components, you must be extra careful when creating copies to or from environments that have Customer Insights - Journeys installed on them.

<a name="copy-warning"></a>

> [!WARNING]
> You cannot do a simple copy of a Customer Insights - Journeys environment like you can with most other Dynamics 365 environments that don't have Customer Insights - Journeys installed. If you do a simple copy without following the steps here, the resulting copy won't work and may render the target environment unrecoverable.

> [!WARNING]
> This procedure will completely delete the target environment. If Customer Insights - Journeys is installed on the target environment, then it will be completely uninstalled (which will release the license) and all data (including interaction records) will be deleted. Even if you backup the target environment first, the backup won't include interaction data or image files. If you need to preserve interaction data and/or images from the target environment, be sure to back up the database for your marketing services, either to blob storage to some other storage media. For more information about how to backup data to blob storage, see [Create custom analytics with Power BI](custom-analytics.md).

> [!IMPORTANT]
> Your copied environment requires its own Customer Insights - Journeys license. If the target environment already has Customer Insights - Journeys installed, the copy will automatically take over that license (you don't have to do anything). If the target environment doesn't have Customer Insights - Journeys installed, we recommend that you have an unused Customer Insights - Journeys license for your tenant before you start the copy, and purchase one if you don't. If you don't have a Customer Insights - Journeys license available before copying, the copy will end in a *disconnected state*, which means that many key features won't work (relevant error messages will be shown). In this case, you can purchase a new Customer Insights - Journeys license and [re-run the setup wizard](re-run-setup.md) to apply it to your new copy.

> [!NOTE]
> If you are copying to a support environment, see [Copy a production environment to a support environment](#support-copy) for instructions. For all other types of copies, continue reading here.

<a name="prepare-source"></a>

### Step 1: Prepare your source environment

The _source environment_ is the Customer Insights - Journeys environment you are copying _from_. To prepare your source environment for copying, [Open the Power Platform admin center](power-platform-admin-center.md) and make sure that the Customer Insights - Journeys application and its related solutions are all up to date on your source environment, as described in [Keep Customer Insights - Journeys up to date](apply-updates.md).

### Step 2: Prepare your target environment

The _target environment_ is the environment you are copying _to_. As with the source environment, you must prepare the target environment before you copy _if Customer Insights - Journeys is installed on the source environment, the target environment, or both_.

> [!NOTE]
> The target environment will almost always be a sandbox environment because copying to a production environment isn't supported (but you can easily [convert a sandbox into a production environment](#switch-sandbox-prod) after copying, if you wish). You must already have the target environment available on your tenant. You will be able to see it on the **Environments** page of the Power Platform admin center. If you don't have one, please  [contact Microsoft Support](/power-platform/admin/get-help-support) for assistance.

To prepare your target environment, do the following _before_ starting the copy:

1. If the Customer Insights - Journeys environment was [integrated with a Power Apps portal](portal-optional.md), reset the portal as described in [Reset a portal](/powerapps/maker/portals/admin/reset-portal). This is important because it will free your portal license to be used elsewhere.
1. After the reset, the portal will still be shown as "configured" in the Power Platform admin center, but you will now be able to select it when you run the Customer Insights - Journeys setup wizard to set up a new, copied, or restored environment.
1. **[Optional]** To free up your DNS zones, remove all DNS records that were created during [domain authentication](mkt-settings-authenticate-domains.md). If you do not remove DNS records during this step, there will be no way to determine which records were used after the environment is copied.
> [!WARNING]
> Do not remove DNS records for domains that are used in other orgs.

### Step 3: Copy the environment

Once your source and target environments are prepared, you're ready to make the copy following the procedure described in [Copy an environment](/power-platform/admin/copy-environment).

Pay special attention when choosing whether to create an [Everything or Customizations and schemas only copy](#target-content).

![Select the copy type.](media/instances-everything-schemas.png "Select the copy type")

### Step 4: Prepare the target environment for use

After creating your copy, you must complete the following steps:

- Make sure the target environment isn't in administration mode. For more information about this setting and how to disable it, see [Administration mode](/power-platform/admin/sandbox-environments).
- Run the Customer Insights - Journeys setup wizard on the target environment. This is needed because the target environment must be set up with a new collection of marketing services (and, in some cases, supporting apps such as Customer Voice and/or Portals). For instructions, see [Run the Customer Insights - Journeys setup wizard](setup.md#run-wizard). If you don't run the setup wizard, then the copy will end in a *disconnected state*, which means that many key features won't work until you do (relevant error messages will be shown).

> [!IMPORTANT]
> After you copy Customer Insights - Journeys to a new environment, you must link the new environment to your domain and authenticate it for emails. To do this, you will need to re-check your domain settings and update the DNS records. Learn more: [Authenticate your domains](mkt-settings-authenticate-domains.md).

## Create and restore backups

As with copy operations, backup and restore operations typically require a few extra steps when Customer Insights - Journeys is installed.

> [!IMPORTANT]
> Backups **do not** include Customer Insights - Journeys services or the data they contain. When you restore a backup, all organizational data, solutions, apps, and customizations will be present, but no interaction data, insights, or previously uploaded files will be available on the restored system. The situation is similar to that of [copying a Customer Insights - Journeys environment](#copy-to-sandbox).

> [!WARNING]
> If you restore data in Customer Insights - Journeys, all consents will return to the state they were in at the time backup was made. This may result in consent data being obsolete. To avoid complications, export all consent data into Excel before starting the restore process and use it as a reference after the restore is completed.

### Automatic system backups

Microsoft automatically makes daily backup copies of all Dynamics 365 environments, including those that have the Customer Insights - Journeys app installed. Like other types of copies and backups, automatic system backups include the full organizational database, but not the interaction records or image files stored in the marketing services. System backups are kept for just a few days and then deleted.

For more information about automatic backups in Dynamics 365, see [System backups](/power-platform/admin/backup-restore-environments#system-backups).

For more information about how to backup marketing-services data to blob storage, see [Create custom analytics with Power BI](custom-analytics.md).

### Create an on-demand backup

You can create an on-demand backup at any time, but when Customer Insights - Journeys is installed on your source environment, you must take a few extra precautions by using the following procedure:

1. [Open the Power Platform admin center](power-platform-admin-center.md) and make sure that the Customer Insights - Journeys application and its related solutions are all up to date on your source environment, as described in [Keep Customer Insights - Journeys up to date](apply-updates.md).
1. Create the on-demand backup as usual, as described in [Backup and restore environments](/power-platform/admin/backup-restore-environments).

    ![Create an on-demand backup.](media/instances-backup.png "Create an on-demand backup")

As with automatic backups, on-demand backups include the full organizational database, but not the interaction records or image files stored in the marketing services. For more information about how to export marketing-services data to blob storage, see [Create custom analytics with Power BI](custom-analytics.md).

### Restore a backup onto another environment

You can easily restore any on-demand or automatic system backup to any available sandbox environment (other than the environment you took the backup from). But as with copy operations, you need to prepare the target environment first.

> [!WARNING]
> This procedure will completely erase the target environment. If Customer Insights - Journeys is installed on the target environment, then it will be completely uninstalled (which will release the license) and all data (including files and interaction records) will be deleted. Even if you backup the target environment first, the backup won't include image files or interaction data, so if you need to preserve these, be sure to back up your marketing-services data, either to blob storage or to other storage media. For more information about how to backup marketing-services data to blob storage, see [Create custom analytics with Power BI](custom-analytics.md).

> [!IMPORTANT]
> Your copied environment requires its own Customer Insights - Journeys license. If the target environment already has Customer Insights - Journeys installed, the copy will automatically take over that license (you don't have to do anything). If the target environment doesn't have Customer Insights - Journeys installed, we recommend that you have an unused Customer Insights - Journeys license for your tenant before you start the copy, and purchase one if you don't. If you don't have a Customer Insights - Journeys license available before copying, the copy will end in a *disconnected state*, which means that many key features won't work (relevant error messages will be shown). In this case, you can purchase a new Customer Insights - Journeys license and [re-run the setup wizard](re-run-setup.md) to apply it to your new copy.

To restore a backup onto a sandbox environment:

1. If your target environment includes a [Power Apps portal](portal-optional.md), then reset the portal as described in [Reset a portal](/powerapps/maker/portals/admin/reset-portal). This is important because it will free your portal license to be used elsewhere. After the reset, the portal will still be shown as "Configured" in the Power Platform admin center, but you will now be able to select it when you run the Customer Insights - Journeys setup wizard to set up a new, copied, or restored environment.

1. Restore the backup onto the newly prepared sandbox as usual, as described in [Backup and restore environments](/power-platform/admin/backup-restore-environments).

1. Prepare the restored environment for use by doing the following:

   - Make sure the restored environment is not in administration mode. For more information about this setting and how to disable it, see [Administration mode](/power-platform/admin/sandbox-environments#administration-mode).
   - Run the Customer Insights - Journeys setup wizard on the target environment. For instructions, see [Run the Customer Insights - Journeys setup wizard](setup.md#run-wizard). If you don't run the setup wizard, the copy will end in a *disconnected state*, which means that many key features won't work until you do (relevant error messages will be shown).

<a name="switch-sandbox-prod"></a>

## Switch an environment between sandbox and production status

Many environment management tasks only allow you to work on a sandbox environment as the source or destination of a copy, backup, or restore operation. However, you can easily switch any environment from sandbox to production, or production to sandbox, at any time. The Customer Insights - Journeys app doesn't limit this standard platform operation. More information: [Change the environment type](/power-platform/admin/switch-environment)

<a name="support-copy"></a>

## Copy a production environment to a support environment

Microsoft Support offers a service for testing pending changes (usually updates) on a copy of your production environment. If you wish to use this service, contact Microsoft Support to find out if you are eligible. If you are eligible, Microsoft Support will create a support environment on your tenant and then ask you to copy your production environment onto it. More information: [Manage Support environments](/power-platform/admin/support-environment)

> [!NOTE]
> When you copy to a support environment, you don't need to make any special preparations that were mentioned in other sections of this article.

> [!IMPORTANT]
> Support environments remain available for 14 days and are then deleted.

To copy a production environment to a support environment:

1. If you don't already have a support environment available, contact Microsoft Support to request one. Once your support environment is available on your tenant, you'll be able to see it in the Power Platform admin center.

1. Select the production environment that you want to copy and then select **Copy** in the top ribbon.

    ![Select the source environment and then choose Copy.](media/instances-copy.png "Select the source environment and then choose Copy")

1. The **Copy environment** pane opens on the right side of the page. Make the following settings:

   - **Copy environment**: This should already show the name of the environment you have chosen to copy at the top of the pane.
   - **Copy over**: Select **Everything**.
   - **Select environment to overwrite**: Select the name of the support environment that was created for you. The name of your support environment includes your case number.

    ![Choose your copy options.](media/instances-overwrite-support4.png "Choose your copy options")

1. When you select the target environment, most of the other settings here are set automatically, and a notice is shown to alert you that Microsoft Support will be able to access the support environment. Read the notice and select **OK** if you agree with the terms.

1. Your production environment is now copied to the support environment.

## Delete or reset a Customer Insights - Journeys environment

For standard Dynamics 365 environments (without Customer Insights - Journeys installed), you can use the Power Platform admin center to delete or reset an environment. However, if you do have Customer Insights - Journeys installed, you should also do the following:

1. If the Customer Insights - Journeys environment was [integrated with a Power Apps portal](portal-optional.md), reset the portal as described in [Reset a portal](/powerapps/maker/portals/admin/reset-portal). This is important because it will free your portal license to be used elsewhere. After the reset, the portal will still be shown as **Configured** in the Power Platform admin center, but you will now be able to select it when you run the Customer Insights - Journeys setup wizard to set up a new, copied, or restored environment.
1. Delete or reset the environment as usual. More information: [Delete environment](/power-platform/admin/delete-environment).

> [!NOTE]
> Your Customer Insights - Journeys license is automatically released when you delete or reset its environment, so you'll be free to install it on another environment.

> [!WARNING]
> When you reset a Customer Insights - Journeys environment, you *must* choose an app template that enables Dynamics apps. Dynamics apps require a special template that contains prerequisite solutions. If the app template you select does not enable Dynamics apps, you will need to delete the environment and provision the Customer Insights - Journeys app into a different environment.

## Change the URL for an environment with Customer Insights - Journeys or outbound marketing installed

For standard Dynamics 365 environments (including Customer Insights - Journeys), you can use the Power Platform admin center to change the URL of the environment. Learn more: [Edit properties of an environment](/power-platform/admin/edit-properties-environment).

[!INCLUDE[footer-include](./includes/footer-banner.md)]
