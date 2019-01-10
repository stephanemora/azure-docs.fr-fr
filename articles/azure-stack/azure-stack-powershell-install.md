---
title: Installer PowerShell pour Azure Stack | Microsoft Docs
description: Découvrez comment installer PowerShell pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: fe64011991732c7493d8efd06516efc664b674a4
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752773"
---
# <a name="install-powershell-for-azure-stack"></a>Installer PowerShell pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour utiliser votre cloud, vous devrez installer des modules PowerShell compatibles avec Azure Stack. La compatibilité est activée via une fonctionnalité appelée *profils d’API*.

Les profils API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell Azure Resource Manager avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack prend en charge une version de profil ayant une date spécifique, comme **2018-03-01-hybrid**. Quand vous installez un profil, les modules PowerShell Azure Resource Manager qui correspondent au profil spécifié sont installés.

Vous pouvez installer les modules PowerShell compatibles avec Azure Stack dans des scénarios connectés à Internet, partiellement connectés, ou déconnectés. Cet article présente les instructions détaillées d’installation de PowerShell pour Azure Stack dans ces scénarios.

## <a name="1-verify-your-prerequisites"></a>1. Vérifier la configuration requise

Avant de démarrer avec Azure Stack et PowerShell, les conditions préalables suivantes doivent être réunies :

- **PowerShell Version 5.0** Pour vérifier votre version, exécutez **$PSVersionTable.PSVersion** et comparez la version **Major**. Si vous n’avez pas PowerShell 5.0, suivez les instructions d’[Installation de PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 nécessite un ordinateur Windows.

- **Exécutez Powershell dans une invite de commandes avec élévation de privilèges**.
  Vous devez exécuter PowerShell avec des privilèges administratifs.

- **Accès à PowerShell Gallery** Vous devez avoir accès à [PowerShell Gallery](https://www.powershellgallery.com). La galerie constitue le référentiel central pour le contenu PowerShell. Le module **PowerShellGet** contient des applets de commande pour la découverte, l’installation, la mise à jour et la publication d’artefacts PowerShell tels que des modules, des ressources DSC, des capacités de rôle et des scripts provenant de PowerShell Gallery et d’autres référentiels privés. Si vous utilisez PowerShell dans un scénario déconnecté, vous devez récupérer des ressources d’un ordinateur disposant d’une connexion à Internet, et les stocker dans un emplacement accessible à votre ordinateur déconnecté.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Vérifier l’accessibilité de PowerShell Gallery

Vérifiez que PSGallery est inscrit en tant que référentiel.

> [!Note]
> Cette étape requiert un accès Internet.

Ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Si le référentiel n’est pas enregistré, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Désinstaller les versions existantes des modules Azure Stack PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules AzureRM PowerShell existants. Vous pouvez les désinstaller en appliquant l’une des deux méthodes suivantes :

1. Pour désinstaller les modules AzureRM PowerShell existants, fermez toutes les sessions PowerShell actives et exécutez les applets de commande suivantes :

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    Si vous rencontrez une erreur du type « Le module est déjà en cours d’utilisation », fermez les sessions PowerShell qui utilisent les modules, puis réexécutez le script ci-dessus.

2. Supprimez tous les dossiers qui commencent par `Azure` ou `Azs.` des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. La suppression de ces dossiers supprime tous les modules PowerShell existants.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Connecté : installer PowerShell pour Azure Stack avec connectivité Internet

Azure Stack nécessite le profil de version d’API **2018-03-01-hybrid** pour Azure Stack version 1808. Le profil est disponible en installant le module **AzureRM.Bootstrapper**. Outre les modules AzureRM, vous devez également installer les modules Azure PowerShell spécifiques d’Azure Stack. Le profil de version d’API et les modules Azure Stack PowerShell dont vous avez besoin varient selon la version d’Azure Stack que vous exécutez.

Exécutez le script PowerShell suivant pour installer ces modules sur votre station de travail de développement :

- Azure Stack 1811 ou ultérieur.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

    Pour profiter des fonctionnalités de stockage supplémentaires (mentionnées dans la section Connecté), téléchargez et installez également les packages suivants.

    ```PowerShell
    # Install the Azure.Storage module version 4.5.0
    Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

    # Install the AzureRm.Storage module version 5.0.4
    Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

    # Remove incompatible storage module installed by AzureRM.Storage
    Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

    # Load the modules explicitly specifying the versions
    Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
    Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
    ```

> [!Note]
> Pour mettre à niveau Azure PowerShell à partir la version **2017-03-09-profile** vers la version **2018-03-01-hybrid**, voir le [Guide de migration](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).


- Azure Stack 1808 ou version ultérieure.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

- Azure Stack 1807 ou version antérieure.

  ```PowerShell
  Install-Module -Name AzureRm.BootStrapper
  Use-AzureRmProfile -Profile 2017-03-09-profile -Force
  Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

Vérifiez l’installation en exécutant la commande suivante :

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Déconnecté : installer PowerShell sans connexion Internet

Dans un scénario déconnecté, vous devez tout d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers le Kit de développement Azure Stack pour l’installation.

Connectez-vous à un ordinateur disposant d’une connexion Internet et utilisez le script suivant pour télécharger les packages Azure Resource Manager et AzureStack, selon votre version d’Azure Stack :

  - Azure Stack 1811 ou ultérieur.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

    Pour profiter des fonctionnalités de stockage supplémentaires (mentionnées dans la section Connecté), téléchargez et installez également les packages suivants.

    ```PowerShell
    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
    ```

  - Azure Stack 1808 ou version ultérieure.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

  - Azure Stack 1807 ou version antérieure.

    > [!Note]
    Pour effectuer une mise à niveau à partir de la version 1.2.11, consultez le [guide de migration](https://aka.ms/azspowershellmigration).

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ```

2. Copiez les packages téléchargés sur un appareil USB.

3. Connectez-vous à la station de travail et copiez les packages du périphérique USB vers un emplacement sur la station de travail.

4. Inscrivez à présent cet emplacement comme référentiel par défaut, puis installez les modules AzureRM et AzureStack à partir de ce référentiel :

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurer PowerShell pour l’utilisation d’un serveur proxy

Dans les scénarios qui requièrent un serveur proxy pour accéder à Internet, vous devez commencer par configurer PowerShell pour l’utilisation d’un serveur proxy existant :

1. Ouvrez une invite PowerShell avec élévation de privilèges.
2. Exécutez les commandes suivantes :

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Étapes suivantes

 - [Télécharger les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md)
 - [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](user/azure-stack-powershell-configure-user.md)
 - [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md)
 - [Gérer les profils de version des API dans Azure Stack](user/azure-stack-version-profiles.md)