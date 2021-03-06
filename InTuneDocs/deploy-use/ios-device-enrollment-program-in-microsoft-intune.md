---
# required metadata

title: Apple DEP management for iOS devices | Microsoft Docs
description: Deploy an enrollment profile that enrolls iOS devices bought through the iOS Device Enrollment Program (DEP) “over the air” to manage Apple devices.
keywords:
author: staciebarker
ms.author: stabar
manager: arob98
ms.date: 12/31/2016
ms.topic: article
ms.prod:
ms.service: microsoft-intune
ms.technology:
ms.assetid: 8ff9d9e7-eed8-416c-8508-efc20fca8578

# optional metadata

#ROBOTS:
#audience:
#ms.devlang:
ms.reviewer: dagerrit
ms.suite: ems
#ms.tgt_pltfrm:
#ms.custom:

---

# Enroll corporate-owned Device Enrollment Program iOS devices
Microsoft Intune can deploy an enrollment profile that enrolls iOS devices that were bought through the Device Enrollment Program (DEP) “over the air.” The enrollment package can include setup assistant options for the device. Devices enrolled through DEP cannot be unenrolled by users.

## Apple DEP management for iOS devices with Microsoft Intune
To manage corporate-owned iOS devices with Apple’s Device Enrollment Program (DEP), your organization must join Apple DEP and get devices through that program. Details of that process are available at:  [https://deploy.apple.com](https://deploy.apple.com). Advantages of the program include hands-free setup of devices without using a USB cable to connect each device to a computer.

Before you can enroll corporate-owned iOS devices with DEP, you need a DEP token from Apple. This token lets Intune sync information about DEP-participating devices that your corporation owns. It also permits Intune to perform Enrollment Profile uploads to Apple and to assign devices to those profiles.

1.  **Start managing iOS devices with Microsoft Intune**</br>
    Before you can enroll iOS Device Enrollment Program (DEP) devices, you must finish enabling iOS management for Intune.

2.  **Get an Encryption Key**</br>
    As an administrative user, open the [Microsoft Intune administration console](http://manage.microsoft.com), go to **Admin** &gt; **Mobile Device Management** &gt; **iOS** &gt; **Device Enrollment Program**, and then choose **Download Encryption Key**. Save the encryption key (.pem) file locally. The .pem file is used to request a trust-relationship certificate from the Apple Device Enrollment Program portal.

      ![Update a device enrollment program token](../media/dev-sa-ios-dep.png)

3.  **Get a Device Enrollment Program token**</br>
    Go to the [Device Enrollment Program Portal](https://deploy.apple.com) (https://deploy.apple.com), and sign in with your company Apple ID. This Apple ID must be used later to renew your DEP token.

    1.  In the [Device Enrollment Program Portal](https://deploy.apple.com), go to **Device Enrollment Program** &gt; **Manage Servers**, and then choose **Add MDM Server**.

    2.  Enter the **MDM Server Name**, and then choose **Next**. The server name is for your reference to identify the mobile device management (MDM) server. It is not the name or URL of the Microsoft Intune server.

    3.  The **Add &lt;ServerName&gt;** dialog box opens. Choose **Choose File…** to upload the .pem file, and then choose **Next**.

    4.  The **Add &lt;ServerName&gt;** dialog box shows a **Your Server Token** link. Download the server token (.p7m) file to your computer, and then choose **Done**.

    This certificate (.p7m) file is used to establish a trust relationship between Intune and Apple’s Device Enrollment Program servers.

4.  **Add the DEP token to Intune**</br>
    In the [Microsoft Intune administration console](http://manage.microsoft.com), go to **Admin** &gt; **Mobile Device Management** &gt; **iOS** &gt; **Device Enrollment Program**, and then choose **Upload the DEP Token**. **Browse** to the certificate (.p7m) file, enter your **Apple ID**, and then choose **Upload**.

5.  **Add the Corporate Device Enrollment Policy**</br>
    In the [Microsoft Intune administration console](http://manage.microsoft.com), go to **Policy** &gt; **Corporate Device Enrollment**, and then choose **Add**.

    Provide **General** details including **Name** and **Description**, and specify whether devices assigned to the profile have user affinity or belong to a group.
      - **Prompt for user affinity**: The device must be affiliated with a user during initial setup before it can be permitted to access company data and email as that user. **User affinity** should be set up for DEP-managed devices that belong to users and need to use the company portal (that is, to install apps). Multifactor authentication (MFA) doesn't work during enrollment on DEP devices with user affinity. After enrollment, MFA works as expected on these devices. 

      > [!NOTE]
      > DEP with user affinity requires WS-Trust 1.3 Username/Mixed endpoint to be enabled to request user token.

      - **No user affinity**: The device is not affiliated with a user. Use this affiliation for devices that do tasks without accessing local user data. Apps that require user affiliation, including the Company Portal app that is used to install line-of-business apps, won’t work.

    You can also **Assign devices to the following group**. Choose **Select...** to choose a group.

    [!INCLUDE[groups deprecated](../includes/group-deprecation.md)]

    Next, enable **Configure Device Enrollment Program settings for this policy** to support DEP.

      ![Setup assistant pane](../media/pol-sa-corp-enroll.png)

     The following settings are available for DEP-managed devices:

     - **Department** - Appears when users tap **About Configuration** during activation
     - **Support phone number** - Appears when the user clicks the **Need Help** button during activation
     - **Preparation mode** - Set during activation and cannot be changed without factory resetting the device:
        - **Unsupervised** - Limited management capabilities
        - **Supervised** - Enables more management options and disables Activation Lock by default
     - **Lock enrollment profile to device** - Set during activation and cannot be changed without a factory reset
        - **Disable** - Allows the management profile to be removed from the **Settings** menu
        - **Enable** - (Requires **Preparation Mode** = **Supervised**) Disables iOS settings that could allow removal of the management profile
     - **Setup Assistant Options** - These optional settings can be set up later in the iOS **Settings** menu.
        - **Passcode** - Prompt for passcode during activation. Always require a passcode unless the device will be secured or have access controlled in some other manner (that is, kiosk mode that restricts the device to one app).
        - **Location Services** - If enabled, Setup Assistant prompts for the service during activation
        - **Restore** - If enabled, Setup Assistant prompts for iCloud backup during activation
        - **Apple ID** - If enabled, iOS will prompt users for an Apple ID when Intune attempts to install an app without an ID. An Apple ID is required to download iOS App Store apps, including those installed by Intune.
        - **Terms and Conditions** - If enabled, Setup Assistant prompts users to accept Apple's terms and conditions during activation
        - **Touch ID** - If enabled, Setup Assistant prompts for this service during activation
        - **Apple Pay** - If enabled, Setup Assistant prompts for this service during activation
        - **Zoom** - If enabled, Setup Assistant prompts for this service during activation
        - **Siri** - If enabled, Setup Assistant prompts for this service during activation
        - **Send diagnostic data to Apple** - If enabled, Setup Assistant prompts for this service during activation
     -  **Enable additional Apple Configurator management** - Set to **Disallow** to prevent syncing files with iTunes or management via Apple Configurator. It's a good idea to choose **Disallow**, export further configurations from Apple Configurator, and then deploy as a Custom iOS configuration profile via Intune instead of using this setting to allow manual deployment with or without a certificate.
        - **Disallow** - Prevents the device from communicating via USB (disables pairing)
        - **Allow** - Allows a device to communicate via USB connection for any PC or Mac
        - **Require certificate** - Allows pairing with a Mac with a certificate imported to the enrollment profile

6.  **Assign DEP Devices for Management**
    Go to the [Device Enrollment Program Portal](https://deploy.apple.com) (https://deploy.apple.com) and sign in with your company Apple ID. Go to  **Deployment Program** &gt; **Device Enrollment Program** &gt; **Manage Devices**. Specify how you will **Choose Devices**, provide device information and specify details by device **Serial Number**, **Order Number**, or **Upload CSV File**. Next, choose **Assign to Server** and choose the &lt;ServerName&gt; specified for Microsoft Intune, and then choose **OK**.

7.  **Synchronize DEP-Managed Devices**
    As an administrative user, open the [Microsoft Intune administration console](http://manage.microsoft.com), go to **Admin** &gt; **Mobile Device Management** &gt; **iOS** &gt; **Device Enrollment Program**, and then choose **Sync now**. A sync request is sent to Apple. To see DEP-managed devices after synchronization, in the [Microsoft Intune administration console](http://manage.microsoft.com) go to **Groups** &gt; **All Devices** &gt; **Corporate Pre-enrolled devices** &gt; **By iOS Serial Number**. In the **By iOS Serial Number** workspace, the **State** for managed devices reads “Not contacted” until the device is powered on and runs the Setup Assistant to enroll the device.

    To comply with Apple’s terms for acceptable DEP traffic, Intune imposes the following restrictions:
     -	A full DEP sync can run no more than once every seven days. During a full sync, Intune refreshes every serial number that Apple has assigned to Intune whether the serial has previously been synced or not. If a full sync is attempted within seven days of the previous full sync, Intune only refreshes serial numbers that are not already listed in Intune.
     -	Any sync request is given 10 minutes to finish. During this time or until the request succeeds, the **Sync** button is disabled.

8.  **Distribute devices to users**
    Your corporate-owned devices can now be distributed to users. When an iOS device is turned on it will be enrolled for management by Intune.

## Changes to Intune group assignments

Starting in December 2016, device group management is moving to Azure Active Directory. After the transition to Azure Active Directory groups, group assignment will not appear in the **Corporate Enrollment Profile** options. Because this change will roll out over a series of months, you might not see the change right away. After moving to the new portal, dynamic device group assignments can be defined based on the Corporate Enrollment Profile names. This process ensures that devices that are assigned to a device group already will automatically enroll in the group with policy and apps deployed. [Learn more about Azure Active Directory groups](https://azure.microsoft.com/documentation/articles/active-directory-accessmanagement-manage-groups/)

### See also
[Prerequisites for enrolling devices](prerequisites-for-enrollment.md)
