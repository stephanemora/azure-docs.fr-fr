---
title: Gérer des groupes d’applications pour Windows Virtual Desktop Preview - Azure
description: Explique comment configurer des locataires Windows Virtual Desktop Preview dans Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2baabe6837d25cce5e9f5d9e1071af8417fe5f4d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401860"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Didacticiel : Gérer des groupes d’applications pour Windows Virtual Desktop Preview

Le groupe d’applications par défaut créé pour un nouveau pool d’hôtes Windows Virtual Desktop Preview publie également l’intégralité du bureau. De plus, vous pouvez créer un ou plusieurs groupes d’applications RemoteApp pour le pool d’hôtes. Suivez ce tutoriel pour créer un groupe d’applications RemoteApp et publier des applications individuelles du menu Démarrer.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un groupe RemoteApp.
> * Accorder l’accès aux applications RemoteApp.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

## <a name="create-a-remoteapp-group"></a>Créer un groupe RemoteApp

1. Exécutez l’applet de commande PowerShell suivante pour créer un groupe RemoteApp vide.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Facultatif) Pour vérifier que le groupe d’applications a été créé, vous pouvez exécuter l’applet de commande suivante pour voir la liste de tous les groupes d’applications pour le pool d’hôtes.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Exécutez l’applet de commande suivante pour obtenir la liste des applications du menu de démarrage sur l’image de machine virtuelle du pool d’hôtes. Notez les valeurs de **FilePath**, d’**IconPath** et d’**IconIndex**, ainsi que d’autres informations importantes concernant l’application que vous voulez publier.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Exécutez l’applet de commande suivante pour publier une nouvelle application RemoteApp dans le groupe d’applications créé à l’étape 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Facultatif) Exécutez l’applet de commande suivante pour installer l’application en fonction d’appalias. appalias devient visible quand vous exécutez la sortie de l’étape 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
   ```

6. Pour vérifier que l’application a été publiée, exécutez l’applet de commande suivante.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Répétez les étapes 1 à 5 pour chaque application que vous voulez publier pour ce groupe d’applications.
8. Exécutez l’applet de commande suivante pour accorder aux utilisateurs l’accès aux applications RemoteApp dans le groupe d’applications.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé vos groupes d’applications, vous créez les principaux de service et vous attribuez des rôles à vos utilisateurs. Pour savoir comment procéder, consultez le tutoriel afin de découvrir comment créer des principaux de service et des attributions de rôles avec PowerShell.

> [!div class="nextstepaction"]
> [Créer des principaux de service et des attributions de rôles avec PowerShell](create-service-principal-role-powershell.md)
