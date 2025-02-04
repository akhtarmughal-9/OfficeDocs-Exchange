---
title: Sample script for standalone EOP settings - multiple tenants
f1.keywords: 
  - NOCSH
ms.author: chrisda
author: chrisda
manager: dansimp
ms.date: 
audience: ITPro
ms.topic: how-to

ms.localizationpriority: medium
ms.assetid: e87e84e1-7be0-44bf-a414-d91d60ed8817
ms.custom: 
  - seo-marvel-apr2020
description: In this article, you'll learn how to use PowerShell to apply configuration settings to your tenants in Microsoft Exchange Online Protection (EOP).
ms.technology: mdo
ms.prod: m365-security
---

# Sample script for applying standalone EOP settings to multiple tenants

The sample PowerShell script in this article is for admins who manage multiple standalone Exchange Online Protection (EOP) tenants. Admins can use the script in this article to view and/or apply their configuration settings to multiple standalone EOP tenants.

## To run a script or cmdlet on multiple tenants

1. If you haven't already, [install the Exchange Online V2 module](/powershell/exchange/exchange-online-powershell-v2#install-and-maintain-the-exo-v2-module).

2. Using an spreadsheet app (for example, Excel), create a .csv file with the following details:

   - **UserName column**: The account that you'll use to connect (for example, `admin@contoso.onmicrosoft.com`).
   - **Cmdlet column**: The cmdlet or command to run (for example, `Get-AcceptedDomain` or `Get-AcceptedDomain | Format-Table Name`).

   The .csv file will look like this:

   ```text
   UserName,Cmdlet
   admin@contoso.onmicrosoft.com,Get-AcceptedDomain | Format-Table Name
   admin@fabrikam.onmicrosoft.com,Get-AcceptedDomain | Format-Table Name
   ```

3. Save the .csv file in a location that's easy to find (for example, c:\scripts\inputfile.csv).

4. Copy the [RunCmdletOnMultipleTenants.ps1](#runcmdletonmultipletenantsps1) script into Notepad, and then save the file to a location that's easy to find (for example, c:\scripts).

5. Run the script by using the following syntax:

   ```powershell
   & "<file path>\RunCmdletOnMultipleTenants.ps1" "<file path>\inputfile.csv"
   ```

   Here's an example:

   ```powershell
   & "c:\scripts\RunCmdletOnMultipleTenants.ps1" "c:\scripts\inputfile.csv"
   ```

6. Each tenant will be logged on to, and the script will be run.

## RunCmdletOnMultipleTenants.ps1

> [!NOTE]
> You might need to modify the `Connect-IPPSSession` line in the script to match your environment (the _ConnectionUri_ value).  For details, see Connect to [Exchange Online Powershell](/powershell/exchange/connect-to-exchange-online-protection-powershell).

```powershell
# This script runs Windows PowerShell cmdlets on multiple tenants.
#
# Usage: RunCmdletOnMultipleTenants.ps1 inputfile.csv
#
# .csv input file sample:
#
# UserName,Cmdlet
# admin@contoso.onmicrosoft.com,Get-AcceptedDomain | FT Name
# admin@fabrikam.onmicrosoft.com,Get-AcceptedDomain | FT Name

# Get the .csv file name as an argument to this script.
$FilePath = $args[0]

# Import the UserName and Cmdlet values from the .csv file.
$CompanyList = Import-CSV $FilePath

# Load the EXO V2 module
Import-Module ExchangeOnlineManagement

# Loop through each entry from the .csv file.
ForEach ($Company in $CompanyList) {

# Get the current entry's UserName.
$UserName = $Company.UserName

# Get the current entry's Cmdlet.
$Cmdlet = $Company.Cmdlet

# Connect to EOP PowerShell by using the current entry's UserName. Prompt for the password.
Connect-IPPSSession -UserPrincipalName $UserName -ConnectionUri https://ps.protection.outlook.com/powershell-liveid/

# Here's where the script to be run on the tenant goes.
# In this example, the cmdlet in the .csv file runs.
Invoke-Expression $Cmdlet

# End the current PowerShell session.
Disconnect-ExchangeOnline -Confirm:$false
}
```
