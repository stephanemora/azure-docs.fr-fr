---
title: Installer PowerShell pour Azure Stack | Microsoft Docs
description: Découvrez comment installer PowerShell pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/18/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b3c09582f5135655640768bcbcbef91750827bfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358888"
---
# <a name="install-powershell-for-azure-stack"></a>Installer PowerShell pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack. Dans ce guide, vous allez découvrir les étapes nécessaires à l’installation de PowerShell pour Azure Stack.

Cet article contient des instructions détaillées sur l’installation de PowerShell pour Azure Stack.

> [!Note]
> Les étapes suivantes nécessitent PowerShell 5.0. Pour vérifier votre version, exécutez $PSVersionTable.PSVersion et comparez la version **Major**.

Les commandes PowerShell pour Azure Stack sont installées par le biais de PowerShell Gallery. Vous pouvez utiliser la procédure suivante pour vérifier si PSGallery est inscrit en tant que référentiel, ouvrir une session PowerShell avec élévation de privilèges et exécuter la commande suivante :

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Si le référentiel n’est pas enregistré, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Cette étape requiert un accès Internet. 

## <a name="uninstall-existing-versions-of-powershell"></a>Désinstaller les versions existantes de PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules Azure Stack PowerShell existants. Vous pouvez les désinstaller en appliquant l’une des deux méthodes suivantes :

 - Pour désinstaller les modules PowerShell existants, fermez toutes les sessions PowerShell actives et exécutez la commande suivante :

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Supprimez des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` tous les dossiers qui commencent par « Azure ». La suppression de ces dossiers supprime tous les modules PowerShell existants.

Les sections suivantes décrivent les étapes nécessaires pour installer PowerShell pour Azure Stack. Vous pouvez installer PowerShell sur Azure Stack exploité dans un scénario connecté, partiellement connecté ou déconnecté.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installer PowerShell dans un scénario connecté (avec connectivité Internet)

Les modules AzureRM compatibles avec Azure Stack sont installés par le biais de profils de version d’API. Azure Stack nécessite la version d’API **2017-03-09-profile**, qui est disponible en installant le module AzureRM.Bootstrapper. Pour en savoir plus sur les profils de version d’API et les applets de commande fournies par ces derniers, consultez la page [Gérer les profils de version d’API](user/azure-stack-version-profiles.md). Outre les modules AzureRM, vous devez également installer les modules Azure PowerShell propres à Azure Stack. Exécutez le script PowerShell suivant pour installer ces modules sur votre station de travail de développement :

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Pour confirmer l’installation, exécutez la commande suivante :

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installer PowerShell dans un scénario déconnecté ou partiellement connecté (avec connectivité Internet limitée)

Dans un scénario déconnecté, vous devez tout d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers le Kit de développement Azure Stack pour l’installation.

> [!IMPORTANT]  
> La version du module PowerShell Azure Stack 1.3.0 est fournie avec une liste des changements importants. Pour effectuer une mise à niveau à partir de la version 1.2.11, consultez le [guide de migration](https://aka.ms/azspowershellmigration).

1. Connectez-vous à un ordinateur où vous avez une connexion Internet et utilisez le script suivant pour télécharger les packages AzureRM et AzureStack sur votre ordinateur local :

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Si vous n’exécutez pas Azure Stack avec la mise à jour 1804 ou une version ultérieure, modifiez la valeur du paramètre **requiredversion** sur `1.2.11`. 

2. Copiez les packages téléchargés vers un périphérique USB.

3. Connectez-vous à la station de travail et copiez les packages du périphérique USB vers un emplacement sur la station de travail.

4. Vous devez maintenant inscrire cet emplacement comme dépôt par défaut et installer les modules AzureRM et AzureStack à partir de ce dépôt :

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Configurer PowerShell pour l’utilisation d’un serveur proxy

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
