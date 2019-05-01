---
title: 'Configuration de l’homologation pour un circuit - ExpressRoute : Azure | Microsoft Docs'
description: Cet article vous guide tout au long des étapes de création et d’approvisionnement de l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer des homologations pour votre circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: e1f94b7b2ab71afaa40831446e5e5aede00db7b1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570851"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Créer et modifier l’homologation pour un circuit ExpressRoute

Cet article vous permet de créer et de gérer la configuration du routage d’un circuit ExpressRoute dans le modèle de déploiement Resource Manager à l’aide du portail Azure. Vous pouvez également vérifier l’état, mettre à jour ou supprimer et « déprovisionner » les homologations d’un circuit ExpressRoute. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interface de ligne de commande Azure](howto-routing-cli.md)
> * [Vidéo - Homologation privée](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. Pour plus d’informations sur les domaines de routage et les homologations, voir [À propos des circuits et homologations](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Pour que vous puissiez exécuter les applets de commande décrites dans les sections suivantes, le circuit ExpressRoute doit être dans un état approvisionné et activé.
* Si vous envisagez d’utiliser un hachage de clé/MD5 partagé, veillez à utiliser sur les deux extrémités du tunnel et limiter le nombre de caractères alphanumériques à un maximum de 25. Caractères spéciaux ne sont pas pris en charge. 

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

1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation Microsoft pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

   ![énumérer l’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
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

   ![sélectionner la ligne d’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configurez l’homologation Microsoft. L’illustration suivante montre un exemple de configuration :

   ![configurer l’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres.

   Si votre circuit bascule à l’état « Validation nécessaire » (comme illustré ci-dessous), vous devez ouvrir un ticket de support pour apporter la preuve de possession des préfixes à notre équipe de support.

   ![Enregistrer la configuration d’homologation Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   Vous pouvez ouvrir un ticket de support directement à partir du portail, comme indiqué dans l’exemple suivant :

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’image suivante :

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Pour afficher les détails de l’homologation Microsoft

Vous pouvez afficher les propriétés de l’homologation en sélectionnant l’homologation Microsoft.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Pour mettre à jour la configuration d’homologation Microsoft

Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Pour supprimer une homologation Microsoft

Vous pouvez supprimer votre configuration d’homologation en sélectionnant l’icône Supprimer, comme illustré ci-dessous :

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Homologation privée Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure

1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

   ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurez l'homologation privée Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

   * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation, sauf pour les numéros 65515 à 65520 (inclus).
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Sélectionnez la ligne d’homologation privée Azure, comme indiqué dans l’exemple suivant :

   ![private](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configurer l’homologation privée Azure. L’illustration suivante montre un exemple de configuration :

   ![configurer l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’exemple suivant :

   ![enregistrer l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Pour afficher les détails d’une homologation privée Azure

Vous pouvez afficher les propriétés d'homologation privée Azure en sélectionnant l'homologation.

![afficher l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Pour mettre à jour la configuration d’homologation privée Azure

Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation.

![mettre à jour l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Pour supprimer une homologation privée Azure

Vous pouvez supprimer votre configuration d’homologation en sélectionnant l’icône Supprimer, comme illustré ci-dessous :

> [!WARNING]
> Vous devez vous assurer que la totalité des réseaux virtuels et des connexions ExpressRoute Global Reach sont supprimés avant d’exécuter cet exemple. 
> 
> 

![supprimer l’homologation privée](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Homologation publique Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit ExpressRoute.

> [!Note]
> L’homologation publique Azure est déconseillée pour les circuits de nouveau. Pour plus d’informations, consultez [l’homologation ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation publique Azure

1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation publique Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

   ![énumérer l’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurez l’homologation publique Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

   * Un sous-réseau /30 pour le lien principal. Ce doit être un préfixe IPv4 public valide. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur. 
   * Un sous-réseau /30 pour le lien secondaire. Ce doit être un préfixe IPv4 public valide. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.
3. Sélectionnez la ligne d’homologation publique Azure, comme illustré ci-dessous :

   ![sélectionner la ligne d’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configurez l’homologation publique. L’illustration suivante montre un exemple de configuration :

   ![Configurer l’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l’exemple suivant :

   ![Enregistrer la configuration d’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Pour afficher les détails d’une homologation publique Azure

Vous pouvez afficher les propriétés d'homologation publique Azure en sélectionnant l'homologation.

![afficher les propriétés d’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Pour mettre à jour la configuration d’homologation publique Azure

Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation.

![sélectionner la ligne d’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Pour supprimer une homologation publique Azure

Vous pouvez supprimer votre configuration d’homologation en sélectionnant l’icône Supprimer, comme indiqué dans l’exemple suivant :

![supprimer l’homologation publique](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).
