---
Order: 50
xref: ccm-upgrade
Title: Upgrade
Description: How to upgrade CCM
RedirectFrom: docs/central-management-setup-upgrade
---

This will guide us through upgrading an existing Chocolatey Central Management installation to newer versions.

> :memo: **NOTE**
>
> Looking for installation instructions? See [Central Management Setup](xref:ccm-setup).

> :warning: **WARNING**
>
> - Unless otherwise noted, please follow these steps in **exact** order. These steps build on each other and need to be completed in order.
> - All deployed components of the CCM packages should **always** be the **SAME VERSION**. The only time you should not have this is when you are in a state of upgrading and that transition time should be quite short.

## Step 0: Backup Your Current CCM Enviornment

We suggest you go through and take a snapshot of your CCM VM before proceeding with an upgrade. If taking a snapshot is not an option, at a bare minimum we recommend creating a local backup of the CentralManagement SQL Database and backing up your appsettings.json file located at `C:\tools\chocolatey-management-web\appsettings.json`.

## Step 1: Download Latest Packages

> :memo: **NOTE**
>
> Make sure you have read over the [CCM Compatibility Matrix](xref:central-management#ccm-component-compatibility-matrix) prior to starting internalization as this will save you some headaches.

Similar to how we internalized in [Setup - Internalize Packages](xref:ccm-setup#step-1-internalize-packages), we need to get the latest editions of everything compatible. Be sure that the versions of packages you have match up with the [Compatibility Matrix](xref:central-management#ccm-component-compatibility-matrix).

```powershell
# To run this, you need Chocolatey for Business installed (chocolatey / chocolatey.extension).

# Update the values and remove the < >
$YourInternalRepositoryPushUrl = '<INSERT REPOSITORY URL HERE>'
$YourInternalRepositoryApiKey = '<YOUR API KEY HERE>'
# You get this from the chocolatey.license.xml file:
$YourBusinessLicenseGuid = '<INSERT C4B LICENSE GUID HERE>'

# Download Chocolatey community related items, no internalization necessary
choco download chocolatey chocolateygui --force --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"

# Download Licensed Packages
## DO NOT RUN WITH `--internalize` and `--internalize-all-urls` - see https://github.com/chocolatey/chocolatey-licensed-issues/issues/155
choco download chocolatey-agent chocolatey.extension chocolatey-management-database chocolatey-management-service chocolatey-management-web --force --source="'https://licensedpackages.chocolatey.org/api/v2/;https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"  --user="'user'" --password="'$YourBusinessLicenseGuid'"

# Push all downloaded packages to your internal repository
Get-ChildItem C:\packages -Recurse -Filter *.nupkg | Foreach-Object { choco push $_.Fullname --source="'$YourInternalRepositoryPushUrl'" --api-key="'$YourInternalRepositoryApiKey'"}
```

## Step 2: Upgrade Central Management Database

> :memo: **NOTE** Please see [Central Management Database Setup](xref:ccm-database) for details about all arguments that can be passed and set.

```powershell
choco upgrade chocolatey-management-database -y
```

> :warning: **WARNING**
>
> If you are using QDE and receive an error about deserializing and padding, see the resolution below.

## Step 3: Setup Central Management Windows Service(s)

> :memo: **NOTE** Please see [Central Management Service Setup](xref:ccm-service) for details about all arguments that can be passed and set.

```powershell
choco upgrade chocolatey-management-service -y
```

> :warning: **WARNING**
>
> - If you passed non-default options for any of the following:
>   - `/Username:` / `/Password:` / `/EnterPassword`
>   - `/PortNumber:`
>   You **will need to pass those items again** for upgrades in current releases of CCM.
> - If you passed a non-default option for the `/CertificateDnsName:` / `/CertificateThumbprint:`, you **may need to pass those items again** under the following conditions:
>   - Your certificate's DNS name does not match `<hostname>*`(a certificate that at least starts with the hostname).

> :memo: **NOTE**
>
> Database details that have not changed will not need to be passed.

There may be additional (new) things you will want to configure. Please see [Central Management Service Setup](xref:ccm-service) for details.

## Step 4: Setup Central Management Website

> :memo: **NOTE** Please see [Central Management Web Setup](xref:ccm-website) for details about all arguments that can be passed and set.

```powershell
choco upgrade chocolatey-management-web -y
```

> :warning: **WARNING**
>
> You may need to adjust permissions/roles for your user if not using the default `ccmadmin` account. Please see the roles and permissions your account has versus what is available in `Administration -> Users`.

## Step 5: Upgrade Agent Machines

> :memo: **NOTE** Please see [Central Management Client Setup](xref:ccm-client) for details about all arguments that can be passed and set.

```powershell
choco upgrade chocolatey-agent -y
```

There may be additional (new) things you will want to configure. Please see [Central Management Client Setup](xref:ccm-client) for details.

> :memo: **NOTE**
>
> This could include the agent(s) on the CCM machine(s).

> :warning: **WARNING**
>
> The Chocolatey Agent installed on the same machine that has the CCM Service installed will share the `centralManagementServiceUrl` setting, so that agent can only report into that CCM Service.

### New Deployments Feature Example

As an example, configuring using Deployments would have the folllowing:

```powershell
# Requires Chocolatey Licensed Extension v2.1.0+, Chocolatey-Agent v0.10.0+, and Chocolatey Central Management v0.2.0+:
choco feature enable --name="'useChocolateyCentralManagementDeployments'"
```

> :warning: **WARNING**
>
> As these features have security considerations (it is enabling cross-machine communication), they must be turned on explicitly.
> If you decide you want to open this up for over the internet communication, you should also set `centralManagementClientCommunicationSaltAdditivePassword` and `centralManagementServiceCommunicationSaltAdditivePassword`.
> For more in-depth configuration options and settings for your endpoints, you can view the [CCM Client Setup page](xref:ccm-client)

## FAQs

### Can I simply upgrade all three CCM packages in the same command?

We strongly advise against it as there is an explicit order that things must be upgraded in. Since CCM components can be installed on separate machines, there is no explicit dependency that can be taken. Just note that running

```powershell
# !!!DO NOT DO THIS!!!
# choco upgrade chocolatey-management-database chocolatey-management-service chocolatey-management-web -y
```

when you have everything on the same box may work, but it may not. Please follow the steps here for best success.

### If I update the license file, do I need to restart my services and web?

Yes, you do need to restart the agents, the service, and the web to pick up the license. Here's a script to handle that:

```powershell
# For CCM versions older than v0.6.0
Get-Process -Name "ChocolateySoftware.ChocolateyManagement.Web.Mvc" -ErrorAction SilentlyContinue | Stop-Process -Force

# For CCM version 0.6.0 and 0.6.1
Get-Process -Name "ChocolateySoftware.ChocolateyManagement.Web.Mvc" -ErrorAction SilentlyContinue | Stop-Process -Force
Stop-Website -Name ChocolateyCentralManagement
Restart-WebAppPool -Name ChocolateyCentralManagement
Start-Website -Name ChocolateyCentralManagement

# For CCM v0.6.2 and up
Stop-Website -Name ChocolateyCentralManagement
Restart-WebAppPool -Name ChocolateyCentralManagement
Start-Website -Name ChocolateyCentralManagement

# Restart Chocolatey Agent and/or CCM service (all versions)
Get-Service chocolatey-* | Restart-Service
```

### Can I use Chocolatey Deployments to upgrade CCM based components?

Likely you absolutely can, just keep in mind that there may be a specific ordering in how you would upgrade everything and adhere to that order. In some instances, you may need to upgrade agents first, then CCM components as once CCM is upgraded it may not be able to talk to the agents. However agents will stop being able to talk to CCM for a small period of time while you are upgrading CCM, but then things will start working again.

### Why does the chocolatey-management-web package upgrade when I upgrade the chocolatey-management-service package?

> :warning: **WARNING**
>
> This only applies to version of CCM _after_ 0.6.0 and _before_ 0.9.0.

When you run the upgrade of the chocolatey-management-service package from a computer that has all the CCM packages installed, you may not expect the chocolatey-management-web package to also be upgraded.

This happens due to the bounded version numbers that are in place for the service and web packages.  Starting with 0.6.0, both the service and web packages have the following dependencies:

```xml
    <dependency id="aspnetcore-runtimepackagestore" version="[3.1.16, 4.0.0)" />
    <dependency id="dotnetcore-windowshosting" version="[3.1.16, 4.0.0)" />
```

Prior to 0.6.0, only the chocolatey-management-web package had the following dependencies:

```xml
    <dependency id="aspnetcore-runtimepackagestore" version="[2.1.5, 3.0.0)" />
    <dependency id="dotnetcore-windowshosting" version="[2.1.5, 3.0.0)" />
```

You will notice that there is an upper bounded version number of 3.0.0, meaning that Chocolatey won't allow a version higher than 3.0.0 of these dependencies to be installed.

In the new 0.6.0 packages for CCM, the upper bounded version number has changed to 4.0.0.  As a result, when running the upgrade command for the chocolatey-management-service package, Chocolatey will also want to install a 4.x.x version of the dependencies, but this _isn't_ allowed due to the constraints on the installed earlier version of the chocolatey-management-web package.  To remedy this, Chocolatey is able to find a newer version of the chocolatey-management-web package which does fit within these new constraints, and therefore the chocolatey-management-web package is also upgraded.

## Common Errors and Resolutions

### ERROR: There was an error deserializing the requested JSON file: C:\ProgramData\chocolatey\lib\chocolatey-management-database\tools\app\appsettings.json Padding is invalid and cannot be removed.

This means that the Chocolatey Unique Machine GUID has been changed since you installed the database, as you might see with some versions of QDE (which might be corrected in a version you have.)

In that case you should run the following script:

```powershell
Remove-Item -Force -Path "$env:ChocolateyInstall\lib\chocolatey-management-database\tools\app\appsettings.json" -ErrorAction SilentlyContinue
choco upgrade chocolatey-management-database -y --package-parameters="'/SqlServerInstance:localhost\SQLEXPRESS'" --source="'c:\choco-setup\packages'"
```

### When I upgrade the website, it wipes out any http port bindings I created

This was an issue in releases prior to upgrading to CCM v0.3.0 - see <https://github.com/chocolatey/chocolatey-licensed-issues/issues/156>.
If you run into this, please recreate the bindings again.

### ERROR: The term ‘Install-SettingsJsonFile’ is not recognized as the name of a cmdlet, function, script file, or operable program.

This is <https://github.com/chocolatey/chocolatey-licensed-issues/issues/161>.

There are two workarounds noted:

* Delete the appsettings.json file prior to upgrade
* Do not pass database details if they have not changed during upgrade.

[Central Management Setup](xref:ccm-setup) | [Chocolatey Central Management](xref:central-management)

### When upgrading the CCM website, Chocolatey reports a lot of errors about files being locked, and the CCM website does not function after the upgrade

This is a known issue for v0.6.0 and v0.6.1 releases, and is resolved as of v0.6.2.
The IIS hosting model changed as of v0.6.0 to use the in-process model, which alters how the website needs to be shut down and restarted.

A quick workaround is to stop the CCM website manually before running the upgrade by running the following in an administrative Windows PowerShell session:

```powershell
Get-Process -Name "ChocolateySoftware.ChocolateyManagement.Web.Mvc" -ErrorAction SilentlyContinue | Stop-Process -Force -PassThru
Stop-Website -Name ChocolateyCentralManagement
Stop-WebAppPool -Name ChocolateyCentralManagement
```

Once the upgrade process has completed, you can ensure the website is properly restarted by running:

```powershell
Start-WebAppPool -Name ChocolateyCentralManagement
Start-Website -Name ChocolateyCentralManagement
```
