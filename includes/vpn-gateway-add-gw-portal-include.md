---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b47f21ba6597380ca79bfce03feb8edd62aa5ae
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439660"
---
1. Dans **Rechercher dans les ressources, services et documents (G+/)** , tapez **passerelle de réseau virtuel**. Recherchez la **passerelle de réseau virtuel** dans les résultats de la recherche et sélectionnez-la.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/search.png" alt-text="Capture d’écran du champ Rechercher." lightbox="./media/vpn-gateway-add-gw-portal/search-expand.png":::

1. Dans la page **Passerelle de réseau virtuel**, sélectionnez **+ Créer**. La page **Créer une passerelle de réseau virtuel** s’ouvre.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/create.png" alt-text="Capture d’écran de la page Passerelles de réseau virtuel avec l’onglet Créer mis en surbrillance.":::
1. Sous l’onglet **Général**, renseignez les valeurs pour **Détails du projet** et **Détails de l’instance**.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/instance-details.png" alt-text="Capture d’écran des champs de l’instance.":::

   * **Abonnement**: Sélectionnez l’abonnement à utiliser dans la liste déroulante.
   * **Groupe de ressources** : Ce paramètre est renseigné automatiquement quand vous sélectionnez votre réseau virtuel sur cette page.
   * **Name** : Nommez votre passerelle. Le nommage de votre passerelle n’est pas identique au nommage d’un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
   * **Région** : Sélectionnez la région où vous voulez créer cette ressource. La région de la passerelle doit être la même que celle du réseau virtuel.
   * **Type de passerelle** : Sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**.
   * **Type de VPN** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
   * **Référence SKU** : sélectionnez la référence SKU de passerelle que vous voulez utiliser dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Veillez à sélectionner une référence (SKU) qui prend en charge les fonctionnalités que vous voulez utiliser. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Génération** : sélectionnez la génération que vous voulez utiliser. Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Réseau virtuel** : sélectionnez le réseau virtuel auquel vous souhaitez ajouter cette passerelle dans la liste déroulante.
   * **Plage d’adresses de sous-réseau de la passerelle** : Ce champ apparaît uniquement si votre réseau virtuel n’a pas de sous-réseau de passerelle. Il est préférable de spécifier /27 ou plus grand (/26, /25, etc.). Ceci permet d’obtenir suffisamment d’adresses IP pour les modifications futures, comme l’ajout d’une passerelle ExpressRoute. Nous vous déconseillons de créer une plage inférieure à /28. Si vous disposez déjà d’un sous-réseau de passerelle, vous pouvez en voir les détails en accédant à votre réseau virtuel. Cliquez sur **Sous-réseaux** pour afficher la plage. Si vous souhaitez modifier la plage, vous pouvez supprimer et recréer le sous-réseau de passerelle.