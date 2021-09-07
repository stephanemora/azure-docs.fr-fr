---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 2403ce53175457d0a433f9a6a9310f93f0165a0d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321889"
---
Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

1. Dans le portail, dans la barre **Rechercher des ressources**, tapez **WAN virtuel** dans la zone de recherche, puis sélectionnez **Entrée**.

1. Sélectionnez **WAN virtuels** parmi les résultats. Dans la page relative aux WAN virtuels, sélectionnez **+ Créer** pour ouvrir la page Créer un WAN.

1. Dans la page **Créer un WAN**, sous l’onglet **Informations de base**, renseignez les champs. Vous pouvez modifier les exemples de valeurs à appliquer à votre environnement.

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="La capture d’écran montre le volet Créer un WAN avec l’onglet Informations de base sélectionné.":::

   * **Abonnement** : sélectionnez l’abonnement à utiliser.
   * **Groupe de ressources** : créez-en un, ou utilisez un groupe de ressources existant.
   * **Emplacement du groupe de ressources** : choisissez un emplacement pour les ressources dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser la ressource WAN que vous créez.
   * **Nom** : tapez le nom de votre réseau WAN.
   * **Type** : de base ou standard. Sélectionnez **Standard**. Si vous sélectionnez le type de base, sachez que les WAN virtuels de base ne peuvent contenir que des hubs de base. Les hubs de base peuvent uniquement être utilisés pour les connexions de site à site.

1. Une fois que vous avez fini de renseigner les champs, au bas de la page, sélectionnez **Vérifier + créer**.

1. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.
