---
title: Personnalisation du flux pour les utilisateurs d’Azure Virtual Desktop (classique) – Azure
description: Guide pratique pour personnaliser le flux pour les utilisateurs d’Azure Virtual Desktop (classique) avec des cmdlets PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7a28185d16f0bc7787eccc76a9f4a0a98d5c6a38
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754148"
---
# <a name="customize-feed-for-azure-virtual-desktop-classic-users"></a>Personnalisation du flux pour les utilisateurs d’Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../customize-feed-for-virtual-desktop-users.md).

Vous pouvez personnaliser le flux pour que les ressources d’application distante et de Bureau à distance apparaissent de façon reconnaissable pour vos utilisateurs.

Si ce n’est pas déjà fait, commencez par [télécharger et importer le module PowerShell Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/) pour l’utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personnaliser le nom d’affichage pour une application distante

Vous pouvez changer le nom d’affichage pour une application distante publiée en définissant le nom convivial. Par défaut, le nom convivial est le même que celui du programme RemoteApp.

Pour récupérer une liste des applications distantes publiées pour un groupe d’applications, exécutez l’applet de commande PowerShell suivante :

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Capture d'écran de la cmdlet PowerShell Get-RDSRemoteApp, dans laquelle les noms Name et FriendlyName sont mis en surbrillance pour personnaliser le nom d'affichage.](../media/get-rdsremoteapp.png)

Pour affecter un nom convivial à une application distante, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Capture d'écran de la cmdlet PowerShell Set-RDSRemoteApp, dans laquelle les noms Name et New FriendlyName sont mis en surbrillance pour personnaliser le nom d'affichage.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personnaliser le nom d’affichage pour un Bureau à distance

Vous pouvez changer le nom d’affichage pour un Bureau à distance publié en définissant un nom convivial. Si vous avez créé manuellement un pool d’hôtes et un groupe d’applications de Bureau via PowerShell, le nom convivial par défaut est « Bureau de session ». Si vous avez créé un pool d’hôtes et un groupe d’applications de Bureau via le modèle Azure Resource Manager de GitHub ou l’offre de la Place de marché Azure, le nom convivial par défaut est le même que le nom du pool d’hôtes.

Pour récupérer la ressource Bureau à distance, exécutez l’applet de commande PowerShell suivante :

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Capture d’écran de l’applet de commande PowerShell Get-RDSRemoteApp avec le nom et le nom convivial en évidence.](../media/get-rdsremotedesktop.png)

Pour affecter un nom convivial à la ressource Bureau à distance, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Capture d’écran de l’applet de commande PowerShell Get-RDSRemoteApp avec le nom et le nouveau nom convivial en évidence.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez personnalisé le flux pour les utilisateurs, vous pouvez vous connecter à un client Azure Virtual Desktop afin de le tester. Pour cela, passez aux guides pratiques Connexion à Azure Virtual Desktop :

 * [Se connecter à partir de Windows 10 ou Windows 7](connect-windows-7-10-2019.md)
 * [Se connecter à partir d’un navigateur web](connect-web-2019.md)
