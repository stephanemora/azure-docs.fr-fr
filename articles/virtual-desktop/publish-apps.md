---
title: Publier des applications intégrées dans Azure Virtual Desktop – Azure
description: Explique comment publier des applications intégrées dans Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: fc704b992100430c247260b730929e6bc049b98d
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753068"
---
# <a name="publish-built-in-apps-in-azure-virtual-desktop"></a>Publier des applications intégrées dans Azure Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/publish-apps-2019.md).

Cet article explique comment publier des applications dans votre environnement Azure Virtual Desktop.

## <a name="publish-built-in-apps"></a>Publier des applications intégrées

Pour publier une application intégrée :

1. Connectez-vous à l’une des machines virtuelles de votre pool hôte.
2. Obtenez le **PackageFamilyName** de l’application que vous souhaitez publier en suivant les instructions fournies dans [cet article](/powershell/module/appx/get-appxpackage).
3. Enfin, exécutez l’applet de commande suivante en remplaçant `<PackageFamilyName>` par le **PackageFamilyName** que vous avez trouvé à l’étape précédente :

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Azure Virtual Desktop prend uniquement en charge la publication d’applications dont les emplacements d’installation commencent par `C:\Program Files\WindowsApps`.

## <a name="update-app-icons"></a>Mettre à jour les icônes d’application

Une fois que vous avez publié une application, elle a l’icône d’application Windows par défaut au lieu de son image d’icône normale. Pour remplacer l’icône par son icône normale, placez l’image de l’icône souhaitée sur un partage réseau. Les formats d’image pris en charge sont PNG, BMP, GIF, JPG, JPEG et ICO.

## <a name="publish-microsoft-edge"></a>Publier Microsoft Edge

Le processus que vous utilisez pour publier Microsoft Edge est un peu différent du processus de publication pour d’autres applications. Pour publier Microsoft Edge avec la page d’accueil par défaut, exécutez l’applet de commande suivante :

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer des flux afin d’organiser le mode d’affichage des applications pour les utilisateurs en consultant l’article [Personnaliser le flux pour les utilisateurs d’Azure Virtual Desktop](customize-feed-for-virtual-desktop-users.md).
- Découvrez la fonctionnalité d’attachement d’application MSIX en consultant l’article [Configurer l’attachement d’application MSIX](app-attach.md).

