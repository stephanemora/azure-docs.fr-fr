---
title: Publier des applications intégrées dans Windows Virtual Desktop – Azure
description: Comment publier des applications modernes dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
ms.openlocfilehash: 26ebcc7f2196267719a5f49aeeb8de00d48b9c3e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483733"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publier des applications intégrées dans Windows Virtual Desktop

Cet article explique comment publier des applications dans votre environnement Windows Virtual Desktop.

## <a name="publish-built-in-apps"></a>Publier des applications intégrées

Pour publier une application intégrée :

1. Connectez-vous à l’une des machines virtuelles de votre pool hôte.
2. Obtenez le **PackageFamilyName** de l’application que vous souhaitez publier en suivant les instructions fournies dans [cet article](https://docs.microsoft.com/powershell/module/appx/get-appxpackage?view=win10-ps).
3. Enfin, exécutez l’applet de commande suivante en remplaçant `<PackageFamilyName>` par le **PackageFamilyName** que vous avez trouvé à l’étape précédente :
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop prend uniquement en charge la publication d’applications dont les emplacements d’installation commencent par `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Mettre à jour les icônes d’application

Une fois que vous avez publié une application, elle a l’icône d’application Windows par défaut au lieu de son image d’icône normale. Pour remplacer l’icône par son icône normale, placez l’image de l’icône souhaitée sur un partage réseau. Les formats d’image pris en charge sont PNG, BMP, GIF, JPG, JPEG et ICO.

## <a name="publish-microsoft-edge"></a>Publier Microsoft Edge

Le processus que vous utilisez pour publier Microsoft Edge est un peu différent du processus de publication pour d’autres applications. Pour publier Microsoft Edge avec la page d’accueil par défaut, exécutez l’applet de commande suivante :

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```



