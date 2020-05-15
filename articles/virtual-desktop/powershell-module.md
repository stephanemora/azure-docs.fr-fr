---
title: Module PowerShell Windows Virtual Desktop - Azure
description: Comment installer et configurer le module PowerShell pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6741c034351099f544c20749eb7c7a39e7932181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195126"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurer le module PowerShell pour Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop.
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le module PowerShell pour Windows Virtual Desktop est intégré au module Azure PowerShell. Cet article vous indique comment configurer le module PowerShell afin de pouvoir exécuter des cmdlets pour Windows Virtual Desktop.

## <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell

Pour commencer à utiliser le module, commencez par installer la [dernière version de PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Actuellement, les cmdlets Windows Virtual Desktop fonctionnent uniquement avec PowerShell Core.

Ensuite, vous devez installer le module DesktopVirtualization à utiliser dans votre session PowerShell.

Exécutez la cmdlet PowerShell suivante en mode élevé pour installer le module :

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Si cette cmdlet ne fonctionne pas, essayez de l’exécuter à nouveau avec des autorisations élevées.

Ensuite, exécutez la cmdlet suivante pour vous connecter à Azure :

```powershell
Connect-AzAccount
```

La connexion à votre compte Azure nécessite un code qui est généré lorsque vous exécutez la cmdlet Connect. Pour vous connecter, accédez à <https://microsoft.com/devicelogin>, entrez le code, puis connectez-vous à l’aide de vos informations d’identification d’administrateur Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Cela vous permet de vous connecter directement à l’abonnement par défaut correspondant à vos informations d’identification d’administrateur.

## <a name="change-the-default-subscription"></a>Changer l’abonnement par défaut

Si vous souhaitez modifier l’abonnement par défaut une fois que vous êtes connecté, exécutez cette cmdlet :

```powershell
Select-AzSubscription -SubscriptionName <preferredsubscriptionname>
```

Lorsque vous sélectionnez un nouvel abonnement à utiliser, vous n’avez pas besoin de spécifier l’ID de cet abonnement dans les cmdlets que vous exécutez par la suite. Par exemple, la cmdlet suivante récupère un hôte de session spécifique sans avoir besoin de l’ID d’abonnement :

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Vous pouvez également modifier des abonnements par cmdlet en ajoutant le nom d’abonnement souhaité en tant que paramètre. La cmdlet suivante est identique à l’exemple précédent, sauf que l’ID d’abonnement a été ajouté en tant que paramètre pour changer l’abonnement utilisé par la cmdlet.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Obtenir des emplacements

Le paramètre d’emplacement est obligatoire pour toutes les cmdlets **New-AzWVD** qui créent des objets.

Exécutez la cmdlet suivante pour obtenir la liste des emplacements pris en charge par votre abonnement :

```powershell
Get-AzLocation
```

La sortie pour **Get-AzLocation** se présente ainsi :

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Une fois que vous connaissez l’emplacement de votre compte, vous pouvez l’utiliser dans une cmdlet. Par exemple, voici une cmdlet qui crée un pool d’hôtes à l’emplacement « southeastasia » :

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré votre module PowerShell, vous pouvez exécuter des cmdlets pour effectuer toutes sortes d’opérations dans Windows Virtual Desktop. Voici quelques-uns des endroits où vous pouvez utiliser votre module :

- Suivez nos [tutoriels sur Windows Virtual Desktop]() pour configurer votre propre environnement Windows Virtual Desktop.
- [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell.md)
- [Configurer la méthode d’équilibrage de charge de Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Configurer le type d’affectation d’un pool d’hôtes de bureau personnel](configure-host-pool-personal-desktop-assignment-type.md)
- Et bien plus !

Si vous rencontrez des problèmes, consultez notre article sur la [résolution des problèmes PowerShell](troubleshoot-powershell.md) pour obtenir de l’aide.

