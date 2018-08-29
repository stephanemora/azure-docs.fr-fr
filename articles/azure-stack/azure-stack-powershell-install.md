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
ms.date: 08/10/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2619f959dbefba84ea1a4d5aa974055998b78b5a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "41946479"
---
# <a name="install-powershell-for-azure-stack"></a>Installer PowerShell pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous devrez installer les modules PowerShell compatibles avec Azure Stack pour travailler avec votre cloud. La compatibilité est activée via une fonctionnalité appelée *profils d’API*.

Les profils API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell Azure Resource Manager avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack prend en charge une version de profil ayant une date spécifique, comme **2017-03-09-profile**, et Azure prend en charge le profil de version d’API **la plus récente**. Quand vous installez un profil, les modules PowerShell Azure Resource Manager qui correspondent au profil spécifié sont installés.

Vous pouvez installer les modules PowerShell compatibles avec Azure Stack dans un scénario connecté à Internet, partiellement connecté, ou déconnecté. Dans cet article, nous vous présentons les instructions détaillées pour installer PowerShell pour Azure Stack dans ces scénarios.

## <a name="1-verify-your-prerequisites"></a>1. Vérifier la configuration requise

Avant de commencer à utiliser Azure Stack et PowerShell, vous devez mettre en place quelques exigences.

- **PowerShell Version 5.0**  
Pour vérifier votre version, exécutez $PSVersionTable.PSVersion et comparez la version **Major**. Si vous n’avez pas PowerShell 5.0, suivez le [lien](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) pour effectuer la mise à niveau vers PowerShell 5.0.

  > [!Note]  
  > PowerShell 5.0 nécessite un ordinateur Windows.

- **Exécuter PowerShell avec élévation de privilèges**  
  Vous devrez être en mesure d’exécuter PowerShell avec des privilèges d’administrateur.

- **Accès à PowerShell Gallery**  
  Vous devrez accéder à [PowerShell Gallery](https://www.powershellgallery.com). La galerie constitue le référentiel central pour le contenu PowerShell. Le module **PowerShellGet** contient des applets de commande pour la découverte, l’installation, la mise à jour et la publication d’artefacts PowerShell tels que des modules, des ressources DSC, des capacités de rôle et des scripts provenant de PowerShell Gallery et d’autres référentiels privés. Si vous utilisez PowerShell dans un scénario déconnecté, vous devrez récupérer des ressources d’un ordinateur disposant d’une connexion à Internet et les stocker dans un emplacement accessible à votre ordinateur déconnecté.


<!-- Nuget? -->

## <a name="2-validate-if-the-powershell-gallery-is-accessible"></a>2. Vérifier que PowerShell Gallery est accessible

Vérifiez que PSGallery est inscrit en tant que référentiel.

> [!Note]  
> Cette étape requiert un accès Internet. 

Ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Si le référentiel n’est pas enregistré, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Désinstaller les versions existantes des modules Azure Stack PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules AzureRM PowerShell existants. Vous pouvez les désinstaller en appliquant l’une des deux méthodes suivantes :

1. Pour désinstaller les modules AzureRM PowerShell existants, fermez toutes les sessions PowerShell actives et exécutez les applets de commande suivantes :

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. Supprimez tous les dossiers qui commencent par `Azure` des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. La suppression de ces dossiers supprime tous les modules PowerShell existants.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Connecté : installer PowerShell pour Azure Stack avec connectivité Internet

Azure Stack nécessite la version d’API **2017-03-09-profile**, qui est disponible en installant le module **AzureRM.Bootstrapper**. Outre les modules AzureRM, vous devez également installer les modules Azure PowerShell propres à Azure Stack. 

Exécutez le script PowerShell suivant pour installer ces modules sur votre station de travail de développement :

  - **Version 1.4.0** (Azure Stack 1804 ou supérieur)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **Version 1.2.11** (avant 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

Vérifiez l’installation en exécutant la commande suivante :

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Déconnecté : installer PowerShell sans connexion Internet

Dans un scénario déconnecté, vous devez tout d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers le Kit de développement Azure Stack pour l’installation.

Connectez-vous à un ordinateur disposant d’une connexion Internet et utilisez le script suivant pour télécharger les packages Azure Resource Manager et AzureStack sur votre ordinateur local, selon votre version d’Azure Stack.


  - **Version 1.3.0** (Azure Stack 1804 ou supérieur)
  
    > [!Note]  
    Pour effectuer une mise à niveau à partir de la version 1.2.11, consultez le [guide de migration](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **Version 1.2.11** (avant 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. Copiez les packages téléchargés vers un périphérique USB.

3. Connectez-vous à la station de travail et copiez les packages du périphérique USB vers un emplacement sur la station de travail.

4. Vous devez maintenant inscrire cet emplacement comme dépôt par défaut et installer les modules AzureRM et AzureStack à partir de ce dépôt :

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurer PowerShell pour l’utilisation d’un serveur proxy

Dans les scénarios qui requièrent un serveur proxy pour accéder à Internet, vous devez d’abord configurer PowerShell pour l’utilisation d’un serveur proxy existant.

1. Ouvrez une invite PowerShell avec élévation de privilèges.
2. Exécutez les commandes suivantes :

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Étapes suivantes

 - [Télécharger les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md)
 - [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](user/azure-stack-powershell-configure-user.md)  
 - [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Gérer les profils de version des API dans Azure Stack](user/azure-stack-version-profiles.md)  
