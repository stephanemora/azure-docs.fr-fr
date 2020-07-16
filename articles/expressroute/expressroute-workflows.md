---
title: 'Azure ExpressRoute : Workflows de configuration de circuit'
description: Cette page vous présente les workflows pour la configuration du circuit ExpressRoute et des peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: 58914709838c72246678ce92005de5ac18695a1f
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86204174"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Workflows ExpressRoute d’approvisionnement du circuit et états du circuit
Cette page vous guide de façon sommaire tout au long des workflows d’approvisionnement du service et de configuration du routage.

![workflow de circuit](./media/expressroute-workflows/expressroute-circuit-workflow.png)

La figure suivante et les étapes correspondantes décrivent les tâches de provisionnement d’un circuit ExpressRoute de bout en bout. 

1. Utilisez PowerShell pour configurer un circuit ExpressRoute. Suivez les instructions de l’article [Création de circuits ExpressRoute](expressroute-howto-circuit-classic.md) pour plus de détails.
2. Commandez la connectivité auprès du fournisseur de services. Ce processus varie. Contactez votre fournisseur de connectivité pour plus d’informations sur la commande de connectivité.
3. Assurez-vous que le circuit a été correctement approvisionné en vérifiant l’état approvisionnement du circuit ExpressRoute via PowerShell. 
4. Configurez les domaines de routage. Si votre fournisseur de connectivité gère une configuration de couche 3, il configurera le routage pour votre circuit. Si votre fournisseur de connectivité offre uniquement des services de couche 2, vous devez configurer le routage conformément aux instructions décrites dans les pages [Conditions requises pour le routage](expressroute-routing.md) et [Configuration du routage](expressroute-howto-routing-classic.md).
   
   * Activer le peering privé Azure : activez ce peering pour vous connecter aux machines virtuelles/services cloud déployés au sein de réseaux virtuels.

   * Activer le peering Microsoft : activez cette option pour accéder aux services en ligne de Microsoft, tels qu’Office 365. Tous les services Azure PaaS sont accessibles via le peering Microsoft.
     
     > [!IMPORTANT]
     > Pour vous connecter à Microsoft, vous devez veiller à utiliser un proxy/appareil edge différent de celui que vous utilisez pour Internet. L’utilisation du même appareil edge à la fois pour ExpressRoute et Internet entraîne un routage asymétrique et provoque des pertes de connectivité sur votre réseau.
     > 
     > 
     
     ![workflows de routage](./media/expressroute-workflows/routing-workflow.png)
5. Liaison de réseaux virtuels à des circuits ExpressRoute - vous pouvez lier des réseaux virtuels à votre circuit ExpressRoute. Suivez les instructions [pour lier des réseaux virtuels](expressroute-howto-linkvnet-arm.md) à votre circuit. Ces réseaux virtuels peuvent figurer dans le même abonnement Azure que le circuit ExpressRoute, ou dans un autre abonnement.

## <a name="expressroute-circuit-provisioning-states"></a>États d’approvisionnement du circuit ExpressRoute
Chaque circuit ExpressRoute comporte deux états :

* État d’approvisionnement du fournisseur de service
* Statut

L'état représente l’état d'approvisionnement de Microsoft. Cette propriété est définie sur Activée lorsque vous créez un circuit ExpressRoute

L'état d’approvisionnement du fournisseur de connectivité représente l'état du côté du fournisseur de connectivité. Il peut afficher *NotProvisioned*, *Provisioning* ou *Provisioned*. Pour que vous puissiez configurer le peering, l’état du circuit ExpressRoute doit indiquer Provisioned (Provisionné).

### <a name="possible-states-of-an-expressroute-circuit"></a>États possibles d'un circuit ExpressRoute
Cette section liste les états possibles d’un circuit ExpressRoute.

**Lors de la création**

Le circuit ExpressRoute signale les états suivants lors de la création des ressources.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Quand le fournisseur de connectivité est en cours d’approvisionnement du circuit**

Le circuit ExpressRoute indique les états suivants lorsque le fournisseur de connectivité procède au provisionnement du circuit.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Quand fournisseur de connectivité a terminé le processus d’approvisionnement**

Le circuit ExpressRoute indique les états suivants une fois que le fournisseur de connectivité a terminé le provisionnement du circuit.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Quand le fournisseur de connectivité est en train d’annuler l’approvisionnement du circuit**

Si le circuit ExpressRoute doit être déprovisionné, le circuit signalera les états suivants une fois que le fournisseur de services aura terminé le déprovisionnement.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Vous pouvez choisir de le réactiver si nécessaire, ou exécuter des applets de commande PowerShell pour supprimer le circuit.  

> [!IMPORTANT]
> Un circuit ne peut pas être supprimé si ServiceProviderProvisioningState a la valeur Provisioning (En cours de provisionnement) ou Provisioned (Provisionné). Le fournisseur de connectivité doit déprovisionner le circuit avant de pouvoir le supprimer. Microsoft continuera de facturer le circuit tant que la ressource de circuit ExpressRoute n’aura pas été supprimée dans Azure.
> 

## <a name="routing-session-configuration-state"></a>État de configuration d’une session de routage
L’état de provisionnement BGP indique si la session BGP a été activée sur le réseau de périmètre Microsoft. L’utilisation du peering privé ou Microsoft doit être activée (Enabled).

Il est important de vérifier l'état de la session BGP, en particulier pour le peering Microsoft. En plus de l'état d’approvisionnement BGP, il existe un autre état appelé *état des préfixes publics publiés*. Les préfixes publics publiés doivent afficher l’état *configured*, pour que la session BGP soit opérationnelle et pour que votre routage fonctionne de bout en bout. 

Si l'état du préfixe public publié indique qu’une *validation est nécessaire* , la session BGP n'est pas activée car les préfixes publiés ne correspondent pas au numéro AS dans un des registres de routage. 

> [!IMPORTANT]
> Si l’état des préfixes publics publiés indique *manual validation* (validation manuelle), vous devez ouvrir un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et fournir la preuve que vous êtes le propriétaire des adresses IP publiées, ainsi que le numéro système autonome associé.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Configurez votre connexion ExpressRoute.
  
  * [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuration du routage](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

