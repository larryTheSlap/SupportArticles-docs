---
title: Installing MBAM Compliance and Audit Reports fails
description: Solves an error that occurs when you install Microsoft BitLocker Administration and Monitoring (MBAM) Compliance and Audit Reports feature.
ms.date: 09/11/2020
author: Deland-Han
ms.author: delhan
manager: dscontentpm
audience: itpro
ms.topic: troubleshooting
ms.prod: windows-server
localization_priority: medium
ms.reviewer: manojse, hisingh, avicar, kaushika
ms.prod-support-area-path: Bitlocker
ms.technology: windows-server-security
---
# MBAM setup fails if SQL SSRS is not configured properly

This article provides help to fix an error that occurs when you install Microsoft BitLocker Administration and Monitoring (MBAM) Compliance and Audit Reports feature.

_Original product version:_ &nbsp; Windows 10 - all editions, Windows Server 2012 R2  
_Original KB number:_ &nbsp; 2668533

## Symptoms

While trying to install MBAM Compliance and Audit Reports feature, you may receive following error:

> Setup was unable to verify prerequisites on the computer.

Additionally, you may receive below error message in MBAM setup logs:

> CheckForPrerequisites CA Exception: Invalid URI: The format of the URI could not be determined.

The MBAM setup logs can be generated by running the MbamSetup.exe with /lvx switch.

Example: `MbamSetup.exe /lvx C:\temp\mbamlog.txt`

## Cause

This issue is caused if SQL Server Reporting Services (SSRS) isn't configured correctly.

OR

If SSRS was installed and not yet fully functional and then installed MBAM.

## Resolution

Follow below steps to resolve this particular issue:

1. Connect to MBAM server where Compliance and Audit Reports Server is installed.
2. Open **Reporting Server Configuration Manager** and connect to **Report Server Instance**.
3. Browse to **Database** and make sure you have a Report Server database. If **Report Server Database** is not there, create a database.
4. Make sure **Report Server Mode** is **Native**.
5. Browse to **Web Service URL** and click on **URLs**, you should be able to access the webpage.
6. Browse to **Report Manager URL** and click on **URLs**, you should be able to access the webpage.
7. Once SQL SSRS is configured properly, then try MBAM Setup and the Pre-requisites check will pass and will not report this error message.

OR

If it SSRS was installed prior to installing MBAM, reinstall MBAM.

## More information

In some cases, SSRS Report Manager was bound to an invalid IP/Port or it was bound to an SSL identity and the SSL certificate was invalid or not configured properly.

If you wish to use SSRS with SSL, please refer to the MSDN documentation [Configure TLS connections on a native mode report server](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server).

Ensure that SSRS is configured correctly with SSL and you are able to browse to the URL.
We have also seen this issue, when SSRS is configured for HTTPS in the configuration file, whereas in the GUI it shows up as HTTP.

To verify that you are not hitting this issue, open the SSRS configuration file located under
\<Installation directory>/Reporting Services/ReportServer/RSReportServer.config and ensure that SecureConnectionLevel property is set to 0 if you use HTTP and is set to 1 or higher if you use SSL.

Below is a snippet from a configuration file where SSRS is configured to use HTTP:

```xml
<Configuration>  
    <Dsn></Dsn>  
    <ConnectionType>Default</ConnectionType>  
    <LogonUser></LogonUser>  
    <LogonDomain></LogonDomain>  
    <LogonCred></LogonCred>  
    <InstanceId></InstanceId>  
    <InstallationID>{00000000-0000-0000-0000-000000000001}</InstallationID>  

    <Add Key="SecureConnectionLevel" Value="0" />  
    <Add Key="CleanupCycleMinutes" Value="10" />  
    <Add Key="SQLCommandTimeoutSeconds" Value="60" />  
    <Add Key="MaxActiveReqForOneUser" Value="20" />  
    <Add Key="DatabaseQueryTimeout" Value="120" />
    <Add Key="RunningRequestsScavengerCycle" Value="60" />  
```

To know more about SQL SSRS configuration file and SecureConnectionLevel property refer to following article:

[RsReportServer.config Configuration File](/sql/reporting-services/report-server/rsreportserver-config-configuration-file)