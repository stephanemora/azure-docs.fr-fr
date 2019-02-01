---
title: Utilisation de profils de version des API avec PowerShell dans Azure Stack | Microsoft Docs
description: En savoir plus sur l’utilisation de profils de version des API avec PowerShell dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238114"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Utiliser des profils de version des API pour PowerShell dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les profils de version des API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell AzureRM avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack prend en charge une version de profil ayant une date spécifique, comme **2018-03-01-hybrid**, et Azure prend en charge le profil de version d’API **la plus récente**. Quand vous installez un profil, les modules PowerShell AzureRM qui correspondent au profil spécifié sont installés.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installer le module PowerShell nécessaire pour utiliser des profils de version d’API

Le module **AzureRM.Bootstrapper** qui est disponible par le biais de PowerShell Gallery fournit des applets de commande PowerShell nécessaires pour utiliser des profils de version d’API. Utilisez l’applet de commande suivante pour installer le module **AzureRM.Bootstrapper** :

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Version Azure Stack et versions du profil

Le tableau suivant répertorie le profil de version d’API nécessaire et le moniker du module administrateur PowerShell utilisés dans les versions récentes d’Azure Stack. Si vous utilisez cet article avec une version antérieure à 1808, mettez à jour la version de votre profil ainsi que votre moniker.

| Version N°. | Profil de version d’API | Moniker du module administrateur PS |
| --- | --- | --- |
| Version 1811 ou ultérieure | 2018-03-01-hybrid | 1.6.0 |
| Version 1808 ou ultérieure | 2018-03-01-hybrid | 1.5.0 |
| Version 1804 ou ultérieure | 2017-03-09-profile | 1.4.0 |
| Versions antérieures à 1804 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> Pour mettre à niveau à partir de la version 1.2.11, consultez le [guide de migration](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Installer un profil

Utilisez la cmdlet **Install-AzureRmProfile** avec le profil de version d’API **2018-03-01-hybrid** pour installer les modules AzureRM exigés par Azure Stack. Les modules d’opérateur Azure Stack ne sont pas installés avec ce profil de version d’API. Ils doivent être installés séparément, comme spécifié à l’étape 3 de l’article [Installer PowerShell pour Azure Stack](../azure-stack-powershell-install.md).

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Installer et importer des modules dans un profil

Utilisez l’applet de commande **Use-AzureRmProfile** pour installer et importer les modules associés à un profil de version d’API. Vous pouvez importer un seul profil de version d’API dans une session PowerShell. Pour importer un autre profil de version d’API, vous devez ouvrir une nouvelle session PowerShell. L’applet de commande **Use-AzureRMProfile** effectue les tâches suivantes :

1. Vérifie si les modules PowerShell associés au profil de version d’API spécifié sont installés dans l’étendue actuelle.  
2. Télécharge et installe les modules s’ils ne le sont pas déjà.
3. Importe les modules dans la session PowerShell active.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Pour installer et importer les modules AzureRM sélectionnés à partir d’un profil de version d’API, exécutez l’applet de commande **Use-AzureRMProfile** avec le paramètre **Module** :

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obtenir les profils installés

Utilisez l’applet de commande **Get-AzureRmProfile** pour obtenir la liste des profils de version d’API disponibles :

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Mettre à jour des profils

Utilisez l’applet de commande **Update-AzureRmProfile** pour mettre à jour les modules d’un profil de version d’API vers la version la plus récente des modules disponibles dans PSGallery. Nous vous recommandons de toujours exécuter l’applet de commande **Update-AzureRmProfile** dans une nouvelle session PowerShell pour éviter les conflits lors de l’importation de modules. L’applet de commande **Update-AzureRmProfile** exécute les tâches suivantes :

1. Vérifie si les versions les plus récentes des modules sont installées dans le profil de version d’API donné pour l’étendue actuelle.  
2. Vous invite à les installer si elles ne le sont pas déjà.  
3. Installe et importe les modules mis à jour dans la session PowerShell active.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

Cette applet de commande exécute les tâches suivantes :  

1. Vérifie si les versions les plus récentes des modules sont installées dans le profil de version d’API donné pour l’étendue actuelle.  
2. Supprime du profil de version d’API actuel et de la session PowerShell active les versions antérieures des modules.  
3. Vous invite à installer la version la plus récente.  
4. Installe et importe les modules mis à jour dans la session PowerShell active.  

## <a name="uninstall-profiles"></a>Désinstaller des profils

Utilisez l’applet de commande **Uninstall-AzureRmProfile** pour désinstaller le profil de version d’API spécifié.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Étapes suivantes

* [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md)
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)  
