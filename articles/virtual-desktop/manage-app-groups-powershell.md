---
title: Gérer des groupes d’applications pour Azure Virtual Desktop - Azure
description: Comment gérer des groupes d’applications Azure Virtual Desktop avec PowerShell ou Azure CLI.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f323819492fe89f7742c6b218afa4d2e1bf1b6c0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103927"
---
# <a name="manage-app-groups-using-powershell-or-the-azure-cli"></a>Gérer les groupes d’applications avec PowerShell ou Azure CLI

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (Classic) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Le groupe d’applications par défaut créé pour un nouveau pool d’hôtes Azure Virtual Desktop publie également l’intégralité du bureau. De plus, vous pouvez créer un ou plusieurs groupes d’applications RemoteApp pour le pool d’hôtes. Suivez ce tutoriel pour créer un groupe d’applications RemoteApp et publier des applications individuelles du menu **Démarrer**.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un groupe RemoteApp.
> * Accorder l’accès aux programmes RemoteApp.

## <a name="prerequisites"></a>Prérequis

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Cet article suppose que vous avez suivi les instructions de l’article [Configurer le module PowerShell](powershell-module.md) pour configurer votre module PowerShell et vous connecter à votre compte Azure.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cet article suppose que vous avez déjà configuré votre environnement pour Azure CLI et que vous vous êtes connecté à votre compte Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="create-a-remoteapp-group"></a>Créer un groupe RemoteApp

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour créer un groupe RemoteApp avec PowerShell :

1. Exécutez l’applet de commande PowerShell suivante pour créer un groupe d’applications RemoteApp vide.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Facultatif) Pour vérifier que le groupe d’applications a bien été créé, vous pouvez exécuter l’applet de commande suivante pour afficher la liste de tous les groupes d’applications associés au pool d’hôtes.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Exécutez l’applet de commande suivante pour obtenir la liste des applications du menu **Démarrer** de l’image de la machine virtuelle du pool d’hôtes. Notez les valeurs de **FilePath**, d’**IconPath** et d’**IconIndex**, ainsi que d’autres informations importantes concernant l’application que vous voulez publier.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   La sortie doit afficher tous les éléments du menu Démarrer dans un format comme suit :

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Exécutez l’applet de commande suivante pour installer l’application en fonction de son `AppAlias`. `AppAlias` devient visible quand vous exécutez la sortie de l’étape 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (Facultatif) Exécutez l’applet de commande suivante pour publier un nouveau programme RemoteApp dans le groupe d’applications créé à l’étape 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Pour vérifier que l’application a été publiée, exécutez l’applet de commande suivante.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Répétez les étapes 1 à 5 pour chaque application que vous voulez publier pour ce groupe d’applications.
8. Exécutez l’applet de commande suivante pour accorder aux utilisateurs l’accès aux programmes RemoteApp dans le groupe d’applications.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI ne fournit actuellement aucune commande permettant d’obtenir des applications du menu Démarrer, ni de créer un programme RemoteApp ni de le publier dans le groupe d’applications. Utilisation d’Azure PowerShell

Pour créer un groupe RemoteApp avec Azure CLI :

1. Utilisez la commande [az desktopvirtualization applicationgroup create](/cli/azure/desktopvirtualization##az_desktopvirtualization_applicationgroup_create) pour créer un groupe d’applications distantes :

   ```azurecli
   az desktopvirtualization applicationgroup create --name "MyApplicationGroup" \
      --resource-group "MyResourceGroup" \
      --location "MyLocation" \
      --application-group-type "RemoteApp" \
      --host-pool-arm-path "/subscriptions/MySubscriptionGUID/resourceGroups/MyResourceGroup/providers/Microsoft.DesktopVirtualization/hostpools/MyHostPool"
      --tags tag1="value1" tag2="value2" \
      --friendly-name "Friendly name of this application group" \
      --description "Description of this application group" 
   ```
    
2. (Facultatif) Pour vérifier que le groupe d’applications a bien été créé, vous pouvez exécuter la commande suivante pour afficher la liste de tous les groupes d’applications associés au pool d’hôtes.

   ```azurecli
   az desktopvirtualization applicationgroup list \
      --resource-group "MyResourceGroup"
   ```
---

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes arrivé à ce guide pratique à partir de nos tutoriels, consultez [Créer un pool d’hôtes pour valider les mises à jour de service](create-validation-host-pool.md). Vous pouvez utiliser un pool d’hôtes de validation pour surveiller les mises à jour de service avant de les déployer dans votre environnement de production.