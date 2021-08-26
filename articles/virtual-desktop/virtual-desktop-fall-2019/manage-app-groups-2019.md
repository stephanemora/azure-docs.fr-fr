---
title: Gérer les groupes d’applications pour Azure Virtual Desktop (classique) - Azure
description: Découvrez comment configurer des locataires Azure Virtual Desktop (classique) dans Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6fe87fb7fc0cbe9e727fe1539d8424d9ee1fe1b1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253260"
---
# <a name="tutorial-manage-app-groups-for-azure-virtual-desktop-classic"></a>Tutoriel : Gérer les groupes d’applications pour Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../manage-app-groups.md).

Le groupe d’applications par défaut créé pour un nouveau pool d’hôtes Azure Virtual Desktop publie également l’intégralité du Bureau. De plus, vous pouvez créer un ou plusieurs groupes d’applications RemoteApp pour le pool d’hôtes. Suivez ce tutoriel pour créer un groupe d’applications RemoteApp et publier des applications individuelles du menu **Démarrer**.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un groupe RemoteApp.
> * Accorder l’accès aux programmes RemoteApp.

Avant de commencer, [téléchargez et importez le module PowerShell Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/) pour l’utiliser dans votre session PowerShell, si vous ne l’avez pas déjà fait. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Créer un groupe RemoteApp

1. Exécutez l’applet de commande PowerShell suivante pour créer un groupe d’applications RemoteApp vide.

   ```powershell
   New-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Facultatif) Pour vérifier que le groupe d’applications a bien été créé, vous pouvez exécuter l’applet de commande suivante pour afficher la liste de tous les groupes d’applications associés au pool d’hôtes.

   ```powershell
   Get-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
   ```

3. Exécutez l’applet de commande suivante pour obtenir la liste des applications du menu **Démarrer** de l’image de la machine virtuelle du pool d’hôtes. Notez les valeurs de **FilePath**, d’**IconPath** et d’**IconIndex**, ainsi que d’autres informations importantes concernant l’application que vous voulez publier.

   ```powershell
   Get-RdsStartMenuApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

4. Exécutez l’applet de commande suivante pour installer l’application en fonction de son `AppAlias`. `AppAlias` devient visible quand vous exécutez la sortie de l’étape 3.

   ```powershell
   New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Facultatif) Exécutez l’applet de commande suivante pour publier un nouveau programme RemoteApp dans le groupe d’applications créé à l’étape 1.

   ```powershell
    New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Pour vérifier que l’application a été publiée, exécutez l’applet de commande suivante.

   ```powershell
    Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

7. Répétez les étapes 1 à 5 pour chaque application que vous voulez publier pour ce groupe d’applications.
8. Exécutez l’applet de commande suivante pour accorder aux utilisateurs l’accès aux programmes RemoteApp dans le groupe d’applications.

   ```powershell
   Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer des groupes d’applications, à les remplir avec des programmes RemoteApp et à y affecter des utilisateurs. Pour savoir comment créer un pool d’hôtes de validation, consultez le didacticiel suivant. Vous pouvez utiliser un pool d’hôtes de validation pour surveiller les mises à jour de service avant de les déployer dans votre environnement de production.

> [!div class="nextstepaction"]
> [Créer un pool d’hôtes pour valider les mises à jour de service](create-validation-host-pool-2019.md)
