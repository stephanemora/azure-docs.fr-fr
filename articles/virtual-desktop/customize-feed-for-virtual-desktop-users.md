---
title: Personnaliser le flux pour les utilisateurs de Windows Virtual Desktop - Azure
description: Comment personnaliser le flux pour les utilisateurs de Windows Virtual Desktop avec des applets de commande PowerShell
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7ae4e4916333439956216c11e0c7e738b4e2fff1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527776"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Personnaliser le flux pour les utilisateurs de Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez personnaliser le flux pour que les ressources d’application distante et de Bureau à distance apparaissent de façon reconnaissable pour vos utilisateurs.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà téléchargé et installé le module PowerShell pour Windows Virtual Desktop. Si ce n’est pas le cas, suivez les instructions de l’article [Configurer le module PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personnaliser le nom d’affichage pour une application distante

Vous pouvez changer le nom d’affichage pour une application distante publiée en définissant le nom convivial. Par défaut, le nom convivial est le même que celui du programme RemoteApp.

Pour récupérer une liste des applications distantes publiées pour un groupe d’applications, exécutez l’applet de commande PowerShell suivante :

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Pour attribuer un nom convivial à une application distante, exécutez l’applet de commande suivante avec les paramètres requis :

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Par exemple, imaginons que vous avez récupéré les applications actuelles avec l’exemple d’applet de commande suivant :

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

La sortie ressemblerait à ceci :

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Pour mettre à jour le nom convivial, exécutez cette applet de commande :

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Pour vérifier que vous avez correctement mis à jour le nom convivial, exécutez cette applet de commande :

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

L’applet de commande doit générer la sortie suivante :

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personnaliser le nom d’affichage pour un Bureau à distance

Vous pouvez changer le nom d’affichage pour un Bureau à distance publié en définissant un nom convivial. Si vous avez créé manuellement un pool d’hôtes et un groupe d’applications de Bureau via PowerShell, le nom convivial par défaut est « Bureau de session ». Si vous avez créé un pool d’hôtes et un groupe d’applications de Bureau via le modèle Azure Resource Manager de GitHub ou l’offre de la Place de marché Azure, le nom convivial par défaut est le même que le nom du pool d’hôtes.

Pour récupérer la ressource Bureau à distance, exécutez l’applet de commande PowerShell suivante :

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Pour affecter un nom convivial à la ressource Bureau à distance, exécutez l’applet de commande PowerShell suivante :

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personnaliser un nom d’affichage dans le portail Azure

Vous pouvez changer le nom d’affichage pour un Bureau à distance publié en définissant un nom convivial à l’aide du portail Azure.

1. Connectez-vous au portail Azure sur <https://portal.azure.com>.

2. Recherchez **Windows Virtual Desktop**.

3. Sous services, sélectionnez **Windows Virtual Desktop**.

4. Dans la page Windows Virtual Desktop, sélectionnez **Groupes d’applications** sur le côté gauche de l’écran, puis sélectionnez le nom du groupe d’applications que vous souhaitez modifier.

5. Sélectionnez **Applications** dans le menu sur le côté gauche de l’écran.

6. Sélectionnez l’application que vous souhaitez mettre à jour, puis entrez un nouveau **Nom d’affichage**.

7. Sélectionnez **Enregistrer**. L’application que vous avez modifiée doit maintenant afficher le nom mis à jour.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez personnalisé le flux pour les utilisateurs, connectez-vous à un client Windows Virtual Desktop pour faire des tests. Pour cela, passez aux rubriques de procédures Se connecter à Windows Virtual Desktop :

 * [Se connecter avec Windows 10 ou Windows 7](connect-windows-7-10.md)
 * [Se connecter avec le client web](connect-web.md)
 * [Se connecter avec le client Android](connect-android.md)
 * [Se connecter avec le client iOS](connect-ios.md)
 * [Se connecter avec le client macOS](connect-macos.md)
