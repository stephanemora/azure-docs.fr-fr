---
title: Tutoriel - Créer et modifier un circuit avec ExpressRoute
description: Dans ce tutoriel, vous découvrez comment créer, provisionner, vérifier, mettre à jour, supprimer et déprovisionner un circuit ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: e0ba14cd5db47c12435b2de35d0753b402c947ea
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566260"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Tutoriel : Création et modification d’un circuit ExpressRoute

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Modèle Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-circuit-classic.md)
>

Ce tutoriel vous montre comment créer un circuit ExpressRoute à l’aide du Portail Azure et du modèle de déploiement Azure Resource Manager. Vous pouvez également vérifier l’état d’un circuit, le mettre à jour, le supprimer ou le déprovisionner.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’un circuit ExpressRoute
> * Obtenir l'état actuel d’un circuit
> * Modifier un circuit
> * Déprovisionner et supprimer un circuit

## <a name="before-you-begin"></a>Avant de commencer

* Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vérifiez que vous avez accès au [portail Azure](https://portal.azure.com).
* Assurez-vous que vous disposez des autorisations nécessaires pour créer des ressources réseau. Contactez votre administrateur de compte si vous n'avez pas les autorisations appropriées.
* Vous pouvez [visualiser une vidéo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) avant de commencer afin de mieux comprendre les étapes.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Créer et approvisionner un circuit ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Connectez-vous au portail Azure.

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Créez un circuit ExpressRoute

> [!IMPORTANT]
> Votre circuit ExpressRoute est facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.

Vous pouvez créer un circuit ExpressRoute en sélectionnant l'option permettant de créer une ressource. 

1. Dans le menu du portail Azure, sélectionnez **Créer une ressource**. Sélectionnez **Réseaux** > **ExpressRoute**, comme illustré dans l’image suivante :

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Création d’un circuit ExpressRoute":::

2. Après avoir cliqué sur **ExpressRoute**, vous voyez s’afficher la page **Créer ExpressRoute**. Indiquez le **groupe de ressources**, la **région** et le **nom** du circuit. Cliquez ensuite sur **Suivant : Configuration >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Configurer le groupe de ressources et la région":::

3. Lorsque vous entrez les valeurs dans cette page, veillez à spécifier le niveau de référence (SKU) approprié (Local, Standard ou Premium) et le modèle de facturation des données (Illimitées ou Limitées).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Configurer le circuit":::
    
    * **Type de port** détermine si vous vous connectez à un fournisseur de services ou directement au réseau global de Microsoft dans un emplacement de peering.
    * **Créer ou importer à partir d’un classique** détermine si un nouveau circuit est en cours de création ou si vous migrez un circuit classique vers ARM.
    * Le **fournisseur** est le service Internet à partir duquel vous allez demander votre service.
    * L’**Emplacement de peering** est l’emplacement physique où vous effectuez le peering auprès de Microsoft.

    > [!IMPORTANT]
    > L’emplacement de peering indique [l’emplacement physique](expressroute-locations.md) où vous effectuez le peering auprès de Microsoft. Cet emplacement n’est **pas** lié à la propriété « Emplacement », qui fait référence à la zone géographique où se trouve le fournisseur de ressources réseau Azure. Bien que ces éléments ne soient pas liés, nous vous conseillons de choisir un fournisseur de ressources réseau géographiquement proche de l’emplacement de peering du circuit.

    * La **référence SKU** détermine si un module complémentaire ExpressRoute local, ExpressRoute standard ou ExpressRoute premium est activé. Vous pouvez spécifier **Local** pour obtenir la référence SKU locale, **Standard** pour obtenir la référence SKU standard ou **Premium** pour le module complémentaire Premium. Notez que vous pouvez changer la référence SKU pour activer le module complémentaire Premium.
    > [!IMPORTANT]
    > Vous ne pouvez pas remplacer la référence SKU de **Standard/Premium** par celle de **Local**.
    
    * Le **modèle de facturation** détermine le type de facturation. Vous pouvez spécifier **Limité** pour un forfait de données limité, et **Illimité** pour un forfait de données illimité. Notez que vous pouvez modifier le type de facturation de **Limité** à **Illimité**.

    > [!IMPORTANT]
    > En revanche, vous ne pouvez pas modifier le type de **Illimité** à **Limité**.

    * **Autoriser les opérations classiques** permet de lier des réseaux virtuels classiques au circuit.

### <a name="3-view-the-circuits-and-properties"></a>3. Afficher les circuits et les propriétés

**Afficher tous les circuits**

Vous pouvez afficher tous les circuits que vous avez créés en sélectionnant **Tous les services > Mise en réseau > circuit ExpressRoute** dans le menu de gauche.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu du circuit ExpressRoute":::

Tous les circuits Expressroute créés dans l’abonnement s’affichent ici.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Liste du circuit ExpressRoute":::

**Afficher les propriétés**

Vous pouvez afficher les propriétés du circuit en sélectionnant celui-ci. Dans la page **Vue d’ensemble** de votre circuit, la clé de service s’affiche dans le champ du même nom. Faites référence à la clé de service de votre circuit, puis transmettez-la au fournisseur de services pour terminer le processus d’approvisionnement. La clé de service est spécifique à votre circuit.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Afficher les propriétés":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement

Dans cette page, **Statut du fournisseur** fournit des informations sur l’état actuel de l’approvisionnement du côté fournisseur de services. **Statut du circuit** indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez l’article [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

État du fournisseur : **Non approvisionné**<BR>
État du circuit : **Activé**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Lancer le processus d’approvisionnement":::

Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous :

État du fournisseur : **Approvisionnement**<BR>
État du circuit : **Activé**

Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

État du fournisseur : **Approvisionné**<BR>
État du circuit : **Activé**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Vérifier régulièrement le statut et l’état de la clé du circuit

Vous pouvez afficher les propriétés du circuit qui vous intéressent en le sélectionnant. Vérifiez le **Statut du fournisseur** et vérifiez qu’il est passé à **Approvisionné** avant de continuer.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Statut du circuit et du fournisseur":::

### <a name="6-create-your-routing-configuration"></a>6. Créer votre configuration de routage

Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) pour créer et modifier des peerings de circuit.

> [!IMPORTANT]
> Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Lier un réseau virtuel à un circuit ExpressRoute

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-arm.md) quand vous travaillez avec le modèle de déploiement Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Récupération de l’état d’un circuit ExpressRoute

Vous pouvez afficher l’état d’un circuit en sélectionnant celui-ci et en affichant la page Vue d’ensemble.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modification d’un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité. Vous pouvez modifier la bande passante, la référence (SKU), le modèle de facturation et autoriser les opérations classiques sur la page **Configuration**. Pour plus d’informations sur les limites et limitations, voir le [FAQ ExpressRoute](expressroute-faqs.md).

Vous pouvez effectuer les tâches suivantes sans temps d’arrêt :

* Activer ou désactiver un module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.

> [!IMPORTANT]
  > Le passage de la référence SKU de **Standard/Premium** à **Local** n’est pas pris en charge.

* Augmenter la bande passante de votre circuit ExpressRoute à condition que la capacité disponible sur le port le permette.

  > [!IMPORTANT]
  > La rétrogradation de la bande passante d'un circuit n'est pas prise en charge.

* Modifiez le plan de mesure de *Données limitées* en *Données illimitées*.

  > [!IMPORTANT]
  > La modification du plan de limitation de **Données illimitées** à **Données limitées** n’est pas prise en charge.

* Vous pouvez activer et désactiver *Autoriser les opérations classiques*.
  > [!IMPORTANT]
  > Vous devrez peut-être recréer le circuit ExpressRoute si la capacité sur le port existant est inappropriée. Vous ne pouvez pas mettre le circuit à niveau si aucune capacité supplémentaire n’est disponible à cet emplacement.
  >
  > Même si vous pouvez aisément mettre à niveau la bande passante, vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à annuler l’approvisionnement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.
  >
  > La désactivation de l’opération du module complémentaire Premium peut échouer si vous utilisez davantage de ressources que ce qui est autorisé pour le circuit standard.

Pour modifier un circuit ExpressRoute, cliquez sur **Configuration**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Modifier le circuit":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Annulation de l’approvisionnement et suppression d’un circuit ExpressRoute

Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est **En cours d’approvisionnement** ou **Approvisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.

> [!NOTE]
>* Vous devez dissocier *tous les réseaux virtuels* du circuit ExpressRoute avant le déprovisionnement. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.
>* Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur **Non approvisionné**), vous pouvez supprimer le circuit. Cette opération arrête la facturation du circuit.

Vous pouvez supprimer votre circuit ExpressRoute en sélectionnant l’icône **Supprimer**. 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Supprimer le circuit":::

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, passez aux étapes suivantes :

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
