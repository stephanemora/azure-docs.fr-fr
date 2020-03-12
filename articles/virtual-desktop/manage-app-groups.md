---
title: Gérer des groupes d’applications pour Windows Virtual Desktop - Azure
description: Explique comment configurer des locataires Windows Virtual Desktop dans Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127816"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Tutoriel : Gérer des groupes d’applications pour Windows Virtual Desktop

Le groupe d’applications par défaut créé pour un nouveau pool d’hôtes Windows Virtual Desktop publie également l’intégralité du bureau. De plus, vous pouvez créer un ou plusieurs groupes d’applications RemoteApp pour le pool d’hôtes. Suivez ce tutoriel pour créer un groupe d’applications RemoteApp et publier des applications individuelles du menu **Démarrer**.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un groupe RemoteApp.
> * Accorder l’accès aux programmes RemoteApp.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) à utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Créer un groupe RemoteApp

1. Exécutez l’applet de commande PowerShell suivante pour créer un groupe d’applications RemoteApp vide.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Facultatif) Pour vérifier que le groupe d’applications a bien été créé, vous pouvez exécuter l’applet de commande suivante pour afficher la liste de tous les groupes d’applications associés au pool d’hôtes.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Exécutez l’applet de commande suivante pour obtenir la liste des applications du menu **Démarrer** de l’image de la machine virtuelle du pool d’hôtes. Notez les valeurs de **FilePath**, d’**IconPath** et d’**IconIndex**, ainsi que d’autres informations importantes concernant l’application que vous voulez publier.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Exécutez l’applet de commande suivante pour installer l’application en fonction de son `AppAlias`. `AppAlias` devient visible quand vous exécutez la sortie de l’étape 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Facultatif) Exécutez l’applet de commande suivante pour publier un nouveau programme RemoteApp dans le groupe d’applications créé à l’étape 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Pour vérifier que l’application a été publiée, exécutez l’applet de commande suivante.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Répétez les étapes 1 à 5 pour chaque application que vous voulez publier pour ce groupe d’applications.
8. Exécutez l’applet de commande suivante pour accorder aux utilisateurs l’accès aux programmes RemoteApp dans le groupe d’applications.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer des groupes d’applications, à les remplir avec des programmes RemoteApp et à y affecter des utilisateurs. Pour savoir comment créer un pool d’hôtes de validation, consultez le didacticiel suivant. Vous pouvez utiliser un pool d’hôtes de validation pour surveiller les mises à jour de service avant de les déployer dans votre environnement de production.

> [!div class="nextstepaction"]
> [Créer un pool d’hôtes pour valider les mises à jour de service](./create-validation-host-pool.md)
