---
Order: 10
xref: c4b-azure-release-notes
Title: Release Notes
Description: Release Notes for the Chocolatey for Business Azure Environment
---

# Chocolatey for Business Azure Environment Release Notes

> :memo: **NOTE**: Issue links may not be publicly available at this time.

## 0.15.0 (May 18, 2022)

Updates the bundled versions of:

* [Chocolatey 1.1.0](https://docs.chocolatey.org/en-us/choco/release-notes#march-30-2022)
* [Chocolatey Licensed Extension 4.1.1](https://docs.chocolatey.org/en-us/licensed-extension/release-notes#april-11-2022)
* [Chocolatey Central Management 0.8.0](https://docs.chocolatey.org/en-us/central-management/release-notes#february-28-2022)
* [Chocolatey GUI 1.0.0](https://docs.chocolatey.org/en-us/chocolatey-gui/release-notes#march-21-2022)
* [Chocolatey GUI Licensed Extension 1.0.0](https://docs.chocolatey.org/en-us/chocolatey-gui-licensed-extension/release-notes#march-21-2022)
* [Chocolatey Agent 1.0.0](https://docs.chocolatey.org/en-us/agent/release-notes#march-21-2022)

### Features

* Provide easy mechanism for update Certificate - see [#78](https://github.com/chocolatey/c4b-azure/issues/78)

### Improvements

* Update to the latest Chocolatey components - see [#77](https://github.com/chocolatey/c4b-azure/issues/77)
* Add Cleanup to Get-UpdatedPackage Jenkins Script - see [#75](https://github.com/chocolatey/c4b-azure/issues/75)
* Packages from the Licensed Feed aren't updated by existing Jenkins Jobs - see [#72](https://github.com/chocolatey/c4b-azure/issues/72)
* Bundled Packages contain x86 and x64 Installers - see [#67](https://github.com/chocolatey/c4b-azure/issues/67)
* Jenkins plugins complain that Jenkins is outdated - see [#65](https://github.com/chocolatey/c4b-azure/issues/65)

### Documentation

* Rename repository to match new name - see [#69](https://github.com/chocolatey/c4b-azure/issues/69)
* Allow users to manage their licenses - see [#62](https://github.com/chocolatey/c4b-azure/issues/62)

### Bundled Version Update

| Package                            | Version            |
|------------------------------------|--------------------|
| chocolatey                         | 1.1.0              |
| chocolatey-compatibility.extension | 1.0.0 (new)        |
| chocolatey-core.extension          | 1.4.0              |
| chocolatey-agent                   | 1.0.0              |
| chocolatey-extension               | 4.1.1              |
| chocolateygui                      | 1.0.0              |
| chocolateygui.extension            | 1.0.0              |
| chocolatey-management-database     | 0.8.0              |
| chocolatey-management-service      | 0.8.0              |
| chocolatey-management-web          | 0.8.0              |
| jenkins                            | 2.332.3            |
| nexus-repository                   | 3.38.3.01          |
| Temurinjre                         | 18.0.1.1000        |
| Temurin11jre                       | 11.0.15.1000 (new) |
| vcredist140                        | 14.32.31326        |

## 0.14.0 (February 14, 2022)

Updates the bundled versions of:

* [Chocolatey 0.12.1](https://docs.chocolatey.org/en-us/choco/release-notes#january-25th-2022)
* [Chocolatey Central Management 0.7.0](https://docs.chocolatey.org/en-us/central-management/release-notes#november-17th-2021)
* [Chocolatey GUI 0.20.0](https://docs.chocolatey.org/en-us/chocolatey-gui/release-notes#february-10-2022).

### Breaking Changes

* Internalize Jenkins Plugins - see [#49](https://github.com/chocolatey/c4b-azure/issues/49)
    In scenarios where the Jenkins plugins were unavailable to download, the deployment would previously fail.
    We have now changed it to contain all of the plugins we require, and to _attempt_ to download the rest.
    This may result in an environment that doesn't fail deployment, but is unlike previous successful deployments.

### Features

* Upgrades bundled software, as above - see [#60](https://github.com/chocolatey/c4b-azure/issues/60)
* Internalizes packages on first run with the deployed Jenkins job - see [#46](https://github.com/chocolatey/c4b-azure/issues/46)
* Adds commas as a valid separator for Jenkins params - see [#50](https://github.com/chocolatey/c4b-azure/issues/50)
* Adds a HTTP->HTTPS redirect - see [#42](https://github.com/chocolatey/c4b-azure/issues/42)
* Changes the deployed VM size to `B4ms` - see [#55](https://github.com/chocolatey/c4b-azure/issues/55)

### Bug Fixes

* Fixes an issue with generated passwords containing quotes - see [#61](https://github.com/chocolatey/c4b-azure/issues/61)
* Deployment now correctly installs internalized version of Chocolatey - see [#59](https://github.com/chocolatey/c4b-azure/issues/59)

### Bundled Version Update

| Package                            | Version         |
|------------------------------------|-----------------|
| chocolatey                         | 0.12.1          |
| chocolatey-agent                   | 0.12.1          |
| chocolatey-extension               | 3.1.0           |
| chocolatey-dotnetfx.extension      | 1.0.1           |
| chocolateygui                      | 0.20.0          |
| chocolateygui.extension            | 0.4.0           |
| chocolatey-management-database     | 0.7.0           |
| chocolatey-management-service      | 0.7.0           |
| chocolatey-management-web          | 0.7.0           |
| dotnetcore-sdk                     | 3.1.410         |
| dotnetfx                           | 4.8.0.20190930  |
| nexus-repository                   | 3.37.3.02       |
| Temurinjre                         | 17.0.1.1200     |
| vcredist140                        | 14.30.30708     |
| AdoptOpenJDKjre                    | N/A (Removed)   |
| DotNet4.5.2                        | N/A (Removed)   |

## 0.13.2 (September 14, 2021)

Updates the bundled version of CCM  to the [recently released 0.6.2](https://docs.chocolatey.org/en-us/central-management/release-notes#august-26th-2021).

### Features

* Upgrades CCM to 0.6.2 - see [#51](https://github.com/chocolatey/c4b-azure/issues/51)

### Bug Fixes

* Fixes an issue with package internalization - see [#54](https://github.com/chocolatey/c4b-azure/issues/54)

### Documentation

* Adds a guide to enabling RDP [here](https://docs.chocolatey.org/en-us/quick-deployment/azure/rdp) - see [#52](https://github.com/chocolatey/c4b-azure/issues/52)
* Adds a guide for onboarding clients [here](https://docs.chocolatey.org/en-us/quick-deployment/azure/client-setup) - see [#53](https://github.com/chocolatey/c4b-azure/issues/53)

### Bundled Version Updates

| Package                            | Version         |
|------------------------------------|-----------------|
| chocolatey-management-database     | 0.6.2           |
| chocolatey-management-service      | 0.6.2           |
| chocolatey-management-web          | 0.6.2           |
| dotnetcore-sdk                     | 3.1.410         |
| vcredist140                        | 14.29.30133     |

## 0.13.1 (August 13, 2021)

Update the bundled version of CCM to the [recently released 0.6.1](https://docs.chocolatey.org/en-us/central-management/release-notes#august-5th-2021).

### Features

* Updates CCM to version 0.6.1, and updates dependencies
* Validates Chocolatey Licence _before_ deploying resources
* Ensured Chocolatey Agent is installed and checking into the CCM installation

### Bug Fixes

* Uses `Restart-WebAppPool` instead of `iisreset`, which fixes a rare issue that failed deployments complaining of insufficient permissions

### Improvements

* Changes the available base packages to offer packages not available in the bundles

### Bundled Version Updates

| Package                            | Version         |
|------------------------------------|-----------------|
| aspnetcore-runtimepackagestore     | 3.1.16          |
| chocolatey-management-database     | 0.6.1           |
| chocolatey-management-service      | 0.6.1           |
| chocolatey-management-web          | 0.6.1           |
| dotnetcore-windowshosting          | 3.1.16          |
| vcredist140                        | 14.29.30040     |

## 0.11.5 (July 29, 2021)

The first released version! Available on the [Azure Marketplace](https://portal.azure.com/#create/chocolateysoftwareinc1605695330527.c4b_azure_qdec4b-qde) from 2021-07-29.

### Features

* QDE Deployment via the Azure Marketplace
* Deploys CCM, Nexus, and Jenkins to a VM
* Takes an SSL certificate and FQDN and configures the environment appropriately

### Documentation

* Adds basic docs [here](https://docs.chocolatey.org/en-us/quick-deployment/azure/)

### Bundled Packages

| Package                            | Version             |
|------------------------------------|---------------------|
| AdoptOpenJDKjre                    | 16.0.1.900          |
| aspnetcore-runtimepackagestore     | 2.2.7               |
| chocolatey                         | 0.10.15             |
| chocolatey.extension               | 2.1.1               |
| chocolatey-agent                   | 0.11.2              |
| chocolatey-core.extension          | 1.3.5.1             |
| chocolateygui                      | 0.18.1              |
| chocolateygui.extension            | 0.2.1               |
| chocolatey-management-database     | 0.5.1               |
| chocolatey-management-service      | 0.5.1               |
| chocolatey-management-web          | 0.5.1               |
| chocolatey-windowsupdate.extension | 1.0.4               |
| DotNet4.5.2                        | 4.5.2.20140902      |
| dotnetcore-windowshosting          | 2.2.7               |
| jenkins                            | 2.222.4             |
| KB2533623                          | 2.0.0               |
| KB2919355                          | 1.0.20160915        |
| KB2919442                          | 1.0.20160915        |
| KB2999226                          | 1.0.20181019        |
| KB3033929                          | 1.0.5               |
| KB3035131                          | 1.0.3               |
| KB3063858                          | 1.0.0               |
| nexus-repository                   | 3.31.1.01           |
| vcredist140                        | 14.29.30037         |
| vcredist2015                       | 14.0.24215.20170201 |
