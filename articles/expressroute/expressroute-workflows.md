---
title: 'Workflows de configuration de circuit - ExpressRoute : Azure| Microsoft Docs'
description: Cette page vous présente les workflows pour la configuration du circuit ExpressRoute et des peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ffcc5ac2193e607573ceb93717258f5349d1f15
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101685"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Workflows ExpressRoute d’approvisionnement du circuit et états du circuit
Cette page vous guide de façon sommaire tout au long des workflows d’approvisionnement du service et de configuration du routage.

![workflow de circuit](./media/expressroute-workflows/expressroute-circuit-workflow.png)

L'illustration et les étapes correspondantes suivantes montrent les tâches que vous devez effectuer pour approvisionner un circuit ExpressRoute de bout en bout. 

1. Utilisez PowerShell pour configurer un circuit ExpressRoute. Suivez les instructions de l’article [Création de circuits ExpressRoute](expressroute-howto-circuit-classic.md) pour plus de détails.
2. Commandez la connectivité auprès du fournisseur de services. Ce processus varie. Contactez votre fournisseur de connectivité pour plus d’informations sur la commande de connectivité.
3. Assurez-vous que le circuit a été correctement approvisionné en vérifiant l’état approvisionnement du circuit ExpressRoute via PowerShell. 
4. Configurez les domaines de routage. Si votre fournisseur de connectivité gère la couche 3 pour vous, il configurera le routage pour votre circuit. Si votre fournisseur de connectivité offre uniquement des services de couche 2, vous devez configurer le routage conformément aux instructions décrites dans les pages [Conditions requises pour le routage](expressroute-routing.md) et [Configuration du routage](expressroute-howto-routing-classic.md).
   
   * Activer l’homologation privée Azure : activez cette homologation pour vous connecter aux machines virtuelles/services cloud déployés au sein de réseaux virtuels.

   * Activer l’homologation Microsoft : activez cette option pour accéder aux services Office 365 et Dynamics 365. De plus, tous les services Azure PaaS sont accessibles via l’homologation Microsoft.
     
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

L'état d’approvisionnement du fournisseur de connectivité représente l'état du côté du fournisseur de connectivité. Il peut afficher *NotProvisioned*, *Provisioning* ou *Provisioned*. Le circuit ExpressRoute doit être dans l'état Provisioned pour pouvoir être utilisé.

### <a name="possible-states-of-an-expressroute-circuit"></a>États possibles d'un circuit ExpressRoute
Cette section répertorie les états possibles d’un circuit ExpressRoute.

**Lors de la création**

Le circuit ExpressRoute affiche l'état suivant dès que vous exécutez l'applet de commande PowerShell pour créer le circuit ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quand le fournisseur de connectivité est en cours d’approvisionnement du circuit**

Le circuit ExpressRoute affiche l'état suivant dès que vous passez la clé de service au fournisseur de connectivité et qu’il démarre le processus d’approvisionnement.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quand fournisseur de connectivité a terminé le processus d’approvisionnement**

Le circuit ExpressRoute affiche l'état suivant dès que le fournisseur de connectivité a terminé le processus d’approvisionnement.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Provisioned et Enabled sont les seuls états dans lesquels le circuit peut se trouver pour pouvoir être utilisé. Si vous utilisez un fournisseur de couche 2, vous pouvez configurer le routage pour votre circuit uniquement lorsqu'il est dans cet état.

**Quand le fournisseur de connectivité est en train d’annuler l’approvisionnement du circuit**

Si vous avez demandé au fournisseur de services d’annuler l’approvisionnement du circuit ExpressRoute, le circuit affichera l’état suivant une fois que le fournisseur de services aura terminé le processus d’annulation de l’approvisionnement.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Vous pouvez choisir de le réactiver si nécessaire, ou exécuter des applets de commande PowerShell pour supprimer le circuit.  

> [!IMPORTANT]
> Si vous exécutez l’applet de commande PowerShell pour supprimer le circuit alors que ServiceProviderProvisioningState a la valeur En cours d’approvisionnement ou Approvisionné, l’opération échoue. Veuillez au préalable contacter votre fournisseur de connectivité pour annuler l’approvisionnement du circuit ExpressRoute, puis supprimer le circuit. Microsoft continuera à facturer le circuit jusqu’à ce que vous exécutiez l’applet de commande PowerShell pour supprimer le circuit.
> 
> 

## <a name="routing-session-configuration-state"></a>État de configuration d’une session de routage
Le protocole d’approvisionnement BGP vous indique si la session BGP a été activée sur le matériel edge Microsoft. L'état doit être activé pour que vous puissiez utiliser l'homologation.

Il est important de vérifier l'état de la session BGP, en particulier pour l'homologation Microsoft. En plus de l'état d’approvisionnement BGP, il existe un autre état appelé *état des préfixes publics publiés*. Les préfixes publics publiés doivent afficher l’état *configured* , à la fois pour que la session BGP soit opérationnelle et pour que votre routage fonctionne de bout en bout. 

Si l'état du préfixe public publié indique qu’une *validation est nécessaire* , la session BGP n'est pas activée car les préfixes publiés ne correspondent pas au numéro AS dans un des registres de routage. 

> [!IMPORTANT]
> Si l'état des préfixes publics publiés indique une *validation manuelle* , vous devez ouvrir un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et fournir la preuve que vous possédez les adresses IP publiés ainsi que le numéro système autonome associé.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Configurez votre connexion ExpressRoute.
  
  * [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuration du routage](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

