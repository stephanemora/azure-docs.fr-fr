---
title: Définir le mode maintenance de Windows Virtual Desktop - Azure
description: Comment configurer et utiliser le mode maintenance dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107508950"
---
# <a name="set-drain-mode"></a>Définir le mode maintenance

Le mode maintenance isole un hôte de session quand vous voulez appliquer des correctifs et effectuer des opérations de maintenance sans interrompre les sessions utilisateur. Quand il est isolé, l’hôte de session n’accepte pas les nouvelles sessions utilisateur. Toutes les nouvelles connexions sont redirigées vers l’hôte de session disponible suivant. Les connexions existantes dans l’hôte de session continuent de fonctionner jusqu’à ce que l’utilisateur se déconnecte ou que l’administrateur mette fin à la session. Quand l’hôte de session est en mode maintenance, les administrateurs peuvent aussi se connecter à distance au serveur sans passer par le service Windows Virtual Desktop. Vous pouvez appliquer ce paramètre à des bureaux groupés et personnels.

## <a name="set-drain-mode-using-the-azure-portal"></a>Définir le mode maintenance en utilisant le portail Azure

Pour activer le mode maintenance dans le portail Azure :

1. Ouvrez le portail Azure et accédez au pool d’hôtes que vous voulez isoler.

2. Dans le menu de navigation, sélectionnez **Hôtes de session**.

3. Sélectionnez ensuite les hôtes pour lesquels vous voulez activer le mode maintenance, puis sélectionnez **Activer le mode maintenance**.

4. Pour désactiver le mode maintenance, sélectionnez les pools d’hôtes sur lesquels le mode maintenance est activé, puis sélectionnez **Désactiver le mode maintenance**.

## <a name="set-drain-mode-using-powershell"></a>Définir le mode maintenance en utilisant PowerShell

Vous pouvez définir le mode maintenance dans PowerShell avec le paramètre *AllowNewSessions*, qui fait partie de la commande [Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true).

Exécutez cette applet de commande pour activer le mode maintenance :

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Exécutez cette applet de commande pour désactiver le mode maintenance :

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Vous devez exécuter cette commande pour chaque hôte de session auquel vous appliquez le paramètre.

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez en savoir plus sur le portail Azure pour Windows Virtual Desktop, consultez [nos tutoriels](create-host-pools-azure-marketplace.md). Si vous êtes déjà familiarisé avec les concepts de base, découvrez les autres fonctionnalités que vous pouvez utiliser avec le portail Azure, comme [Attachement d’application MSIX](app-attach-azure-portal.md) et [Azure Advisor](azure-advisor.md).

Si vous utilisez la méthode PowerShell et que vous voulez voir ce que le module peut faire d’autre, consultez [Configurer le module PowerShell pour Windows Virtual Desktop](powershell-module.md) et nos [Informations de référence sur PowerShell](/powershell/module/az.desktopvirtualization/).