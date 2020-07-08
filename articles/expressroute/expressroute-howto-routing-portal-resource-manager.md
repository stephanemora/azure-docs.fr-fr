---
title: 'Azure ExpressRoute : Configurer le peering'
description: Cet article décrit les étapes de création et d’approvisionnement d’un peering Microsoft et privé ExpressRoute. Cet article montre également comment vérifier l’état, mettre à jour ou supprimer des peerings pour un circuit.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 1e3d855d0860d8d9875e4a93091653cab18965f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738530"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Créer et modifier le peering pour un circuit ExpressRoute

Cet article vous aide à créer et gérer la configuration du routage d’un circuit ExpressRoute Azure Resource Manager (ARM) à l’aide du Portail Azure. Vous pouvez également mettre à jour, supprimer et déprovisionner des peerings d’un circuit ExpressRoute, ainsi qu’en vérifier l’état. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Peering public](about-public-peering.md)
> * [Vidéo - Peering privé](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

Vous pouvez configurer le peering privé et le peering Microsoft pour un circuit ExpressRoute (le peering public Azure est déprécié pour les nouveaux circuits). Les peerings peuvent être configurés dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque peering. Pour plus d’informations sur les domaines de routage et les peerings, consultez [Domaines de routage ExpressRoute](expressroute-circuit-peerings.md). Pour obtenir des informations sur le peering public, consultez [Peering public ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Pour configurer le peering, le circuit ExpressRoute doit être dans un état approvisionné et activé. 
* Si vous prévoyez d’utiliser une clé partagée/le hachage MD5, veillez à l’utiliser aux deux extrémités du tunnel et à limiter le nombre de caractères à 25 maximum. Les caractères spéciaux ne sont pas pris en charge. 

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous. 

> [!IMPORTANT]
> Nous n'annonçons pas de peerings configurés par les fournisseurs de services via le portail de gestion des services. Nous travaillons sur la prochaine activation de cette fonctionnalité. Contactez votre fournisseur de services avant de configurer des peerings BGP.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Peering Microsoft

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration de peering Microsoft pour un circuit ExpressRoute.

> [!IMPORTANT]
> Le peering Microsoft des circuits ExpressRoute qui ont été configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via le peering Microsoft, même si aucun filtre d’itinéraire n’est défini. Le peering Microsoft des circuits ExpressRoute qui sont configurés le 1er août 2017 ou après n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été attaché au circuit. Pour plus d’informations, consultez [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurer un filtre d’itinéraire pour l’homologation Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Pour créer un peering Microsoft

1. Configurer le circuit ExpressRoute. Vérifiez l’**état du fournisseur** pour vous assurer que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer.

   Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer le peering Microsoft pour vous. Dans ce cas, vous n’avez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, effectuez les étapes suivantes.

   **Circuit : état du fournisseur : Non approvisionné**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuit : état du fournisseur : Approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configurez le peering Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.

   * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir ce peering. Assurez-vous qu'aucun autre peering sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour le peering. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Préfixes publiés : Vous devez fournir la liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
   * **Facultatif -** ASN du client : si vous publiez des préfixes non enregistrés dans le numéro AS de peering, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés.
   * Nom du registre de routage : Vous pouvez spécifier les registres RIR/IRR sur lesquels le numéro AS et les préfixes sont inscrits.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Vous pouvez sélectionner le peering que vous souhaitez configurer comme indiqué dans l’exemple suivant. Sélectionnez la ligne de peering Microsoft.

   [![Sélectionner la ligne de Peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Sélectionner la ligne de Peering Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configurez le peering Microsoft. **Enregistrez** la configuration après avoir spécifié tous les paramètres. L’illustration suivante montre un exemple de configuration :

   ![Configurer le peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft vérifie si les « préfixes publics publiés » et « ASN pairs » (ou « ASN client ») spécifiés vous sont attribués dans le registre de routage Internet. Si vous obtenez les préfixes publics d’une autre entité et si l’affectation n’est pas enregistrée avec le registre de routage, la validation automatique ne se termine pas et nécessite une validation manuelle. Si la validation automatique échoue, le message « Validation nécessaire » s’affiche. 
>
> Si vous voyez le message « Validation nécessaire », collectez le ou les documents qui affichent les préfixes publics qui sont attribués à votre organisation par l’entité répertoriée comme propriétaire des préfixes dans le registre de routage et soumettez ces documents pour validation manuelle en ouvrant un ticket de support comme indiqué ci-dessous. 
>

   Si votre circuit obtient un état « Validation nécessaire », vous devez ouvrir un ticket de support pour apporter la preuve de possession des préfixes à notre équipe de support. Vous pouvez ouvrir un ticket de support directement à partir du portail, comme indiqué dans l’exemple suivant :

   ![Validation nécessaire : ticket de support](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’image suivante :

   ![État de peering : Configuré](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "État de Peering : Configuré")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Pour afficher les détails du peering Microsoft

Vous pouvez afficher les propriétés de peering Microsoft en sélectionnant la ligne du peering.

[![Afficher les propriétés de Peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Afficher les propriétés")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Pour mettre à jour la configuration de peering Microsoft

Sélectionnez la ligne du peering que vous souhaitez modifier, puis modifiez les propriétés de peering et enregistrez vos modifications.

![Sélectionner la ligne de peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Pour supprimer le peering Microsoft

Vous pouvez supprimer votre configuration de peering en cliquant sur l’icône Supprimer, comme illustré ci-dessous :

![Supprimer le peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Peering privé Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration de peering privé Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer un peering privé Azure

1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer. 

   Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer le peering privé Azure pour vous. Dans ce cas, vous n’avez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, effectuez les étapes suivantes.

   **Circuit : état du fournisseur : Non approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuit : état du fournisseur : Approvisionné**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configurez le peering privé Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

   * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir ce peering. Assurez-vous qu'aucun autre peering sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour le peering. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour ce peering, sauf pour les numéros 65515 à 65520 (inclus).
   * Vous devez publier les itinéraires depuis votre routeur Edge local vers Azure via BGP quand vous configurez le peering privé.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Sélectionnez la ligne de peering privé Azure, comme indiqué dans l’exemple suivant :

   [![Sélectionner la ligne de Peering privé](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Sélectionner la ligne de Peering privé")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configurer le peering privé Azure. **Enregistrez** la configuration après avoir spécifié tous les paramètres.

   ![configurer le peering privé](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’exemple suivant :

   ![peering privé enregistré](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Pour afficher les détails d’un peering privé Azure

Vous pouvez afficher les propriétés de peering privé Azure en sélectionnant le peering.

[![Afficher les propriétés de Peering privé](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Afficher les propriétés de Peering privé")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Pour mettre à jour la configuration de peering privé Azure

Vous pouvez sélectionner la ligne pour le peering et modifier les propriétés de peering. Après la mise à jour, enregistrez vos modifications.

![mettre à jour le peering privé](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Pour supprimer un peering privé Azure

Vous pouvez supprimer votre configuration de peering en sélectionnant l’icône Supprimer, comme illustré ci-dessous :

> [!WARNING]
> Vous devez vous assurer que la totalité des réseaux virtuels et des connexions ExpressRoute Global Reach sont supprimés avant d’exécuter cet exemple. 
> 
> 

![supprimer le peering privé](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur le peering du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).
