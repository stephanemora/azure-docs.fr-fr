---
title: 'Configuration de l’homologation pour un circuit - ExpressRoute : Azure | Microsoft Docs'
description: Cet article décrit les étapes de création et d’approvisionnement d’un appairage Microsoft et privé ExpressRoute. Cet article montre également comment vérifier l’état, mettre à jour ou supprimer des appairages pour un circuit.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 40ecdb3f83dba741d1430a912a3f17500a36da6e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484331"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Créer et modifier l’homologation pour un circuit ExpressRoute

Cet article vous aide à créer et gérer la configuration du routage d’un circuit ExpressRoute Azure Resource Manager (ARM) à l’aide du Portail Azure. Vous pouvez également mettre à jour, supprimer et déprovisionner des homologations d’un circuit ExpressRoute, ainsi qu’en vérifier l’état. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interface de ligne de commande Azure](howto-routing-cli.md)
> * [Vidéo - Homologation privée](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

Vous pouvez configurer l’appairage Microsoft et privé Azure pour un circuit ExpressRoute (l’appairage public Azure est déconseillé pour les nouveaux circuits). Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. Pour plus d’informations sur les domaines de routage et les homologations, voir [À propos des circuits et homologations](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Pour configurer l’appairage, le circuit ExpressRoute doit être dans un état approvisionné et activé. 
* Si vous prévoyez d’utiliser une clé partagée/le hachage MD5, veillez à l’utiliser aux deux extrémités du tunnel et à limiter le nombre de caractères à 25 maximum. Les caractères spéciaux ne sont pas pris en charge. 

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous. 

> [!IMPORTANT]
> Nous n'annonçons pas d’homologations configurées par les fournisseurs de services via le portail de gestion des services. Nous travaillons sur la prochaine activation de cette fonctionnalité. Contactez votre fournisseur de services avant de configurer des homologations BGP.
> 
> 

## <a name="msft"></a>Homologation Microsoft

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation Microsoft pour un circuit ExpressRoute.

> [!IMPORTANT]
> L’homologation Microsoft des circuits ExpressRoute qui ont été configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via l’homologation Microsoft, même si aucun filtre d’itinéraire n’est défini. L’homologation Microsoft des circuits ExpressRoute qui sont configurés le 1er août 2017 ou après n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été attaché au circuit. Pour plus d’informations, consultez [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurer un filtre d’itinéraire pour l’homologation Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Pour créer une homologation Microsoft

1. Configurer le circuit ExpressRoute. Vérifiez l’**état du fournisseur** pour vous assurer que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer.

   Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation Microsoft pour vous. Dans ce cas, vous n’avez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, effectuez les étapes suivantes.

   **Circuit : état du fournisseur : Non approvisionné**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "État du fournisseur : Non approvisionné")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuit : état du fournisseur : Approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "État du fournisseur = Approvisionné")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configurez l’homologation Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.

   * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Préfixes publiés : Vous devez fournir la liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
   * **Facultatif -** ASN du client : Si vous publiez des préfixes non inscrits au numéro AS de peering, vous pouvez spécifier le numéro AS auquel ils sont inscrits.
   * Nom du registre de routage : Vous pouvez spécifier les registres RIR/IRR sur lesquels le numéro AS et les préfixes sont inscrits.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Vous pouvez sélectionner l’homologation que vous souhaitez configurer comme indiqué dans l’exemple suivant. Sélectionnez la ligne d'homologation Microsoft.

   [![Sélectionner la ligne d’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Sélectionner la ligne d’homologation Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configurez l’homologation Microsoft. **Enregistrez** la configuration après avoir spécifié tous les paramètres. L’illustration suivante montre un exemple de configuration :

   ![Configurer l’appairage Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Si votre circuit obtient un état « Validation nécessaire », vous devez ouvrir un ticket de support pour apporter la preuve de possession des préfixes à notre équipe de support. Vous pouvez ouvrir un ticket de support directement à partir du portail, comme indiqué dans l’exemple suivant :

   ![Validation nécessaire : ticket de support](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’image suivante :

   ![État d’appairage : Configuré](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "État d’appairage : Configuré")]

### <a name="getmsft"></a>Pour afficher les détails de l’homologation Microsoft

Vous pouvez afficher les propriétés d’homologation Microsoft en sélectionnant la ligne de l’homologation.

[![Afficher les propriétés d’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Afficher les propriétés")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Pour mettre à jour la configuration d’homologation Microsoft

Sélectionnez la ligne de l’homologation que vous souhaitez modifier, puis modifiez les propriétés d’homologation et enregistrez vos modifications.

![Sélectionner la ligne d’homologation](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Pour supprimer une homologation Microsoft

Vous pouvez supprimer votre configuration d’homologation en cliquant sur l’icône Supprimer, comme illustré ci-dessous :

![Supprimer l’homologation](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Homologation privée Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure

1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer. 

   Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation privée Azure pour vous. Dans ce cas, vous n’avez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, effectuez les étapes suivantes.

   **Circuit : état du fournisseur : Non approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "État du fournisseur = Non approvisionné")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuit : état du fournisseur : Approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "État du fournisseur = Approvisionné")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configurez l'homologation privée Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

   * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation, sauf pour les numéros 65515 à 65520 (inclus).
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Sélectionnez la ligne d’homologation privée Azure, comme indiqué dans l’exemple suivant :

   [![Sélectionner la ligne d’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Sélectionner la ligne d’homologation privée")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configurer l’homologation privée Azure. **Enregistrez** la configuration après avoir spécifié tous les paramètres.

   ![configurer l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’exemple suivant :

   ![homologation privée enregistrée](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Pour afficher les détails d’une homologation privée Azure

Vous pouvez afficher les propriétés d'homologation privée Azure en sélectionnant l'homologation.

[![Afficher les propriétés d’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Afficher les propriétés d’homologation privée")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Pour mettre à jour la configuration d’homologation privée Azure

Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation. Après la mise à jour, enregistrez vos modifications.

![mettre à jour l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Pour supprimer une homologation privée Azure

Vous pouvez supprimer votre configuration d’homologation en sélectionnant l’icône Supprimer, comme illustré ci-dessous :

> [!WARNING]
> Vous devez vous assurer que la totalité des réseaux virtuels et des connexions ExpressRoute Global Reach sont supprimés avant d’exécuter cet exemple. 
> 
> 

![supprimer l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Homologation publique Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit ExpressRoute.

> [!Note]
> Le peering public Azure est déprécié pour les nouveaux circuits. Pour plus d'informations, consultez [Peering ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Pour afficher les détails d’une homologation publique Azure

Affichez les propriétés d’homologation publique Azure en sélectionnant l’homologation.

### <a name="updatepublic"></a>Pour mettre à jour la configuration d’homologation publique Azure

Sélectionnez la ligne de l’homologation, puis modifiez les propriétés d’homologation.

### <a name="deletepublic"></a>Pour supprimer une homologation publique Azure

Supprimez votre configuration d’homologation en sélectionnant l’icône Supprimer.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).
