---
title: Créer une liaison privée pour la gestion des ressources - Portail Azure
description: Utilisez le portail Azure pour créer une liaison privée pour la gestion des ressources.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 4d829dbc75aa384e57f706f3e689e8b36365a9c1
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137763"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>Utiliser le portail pour créer une liaison privée pour la gestion des ressources Azure

Cet article explique comment vous pouvez utiliser [Azure Private Link](../../private-link/index.yml) pour limiter l’accès pour la gestion des ressources dans vos abonnements. Il illustre l’utilisation du portail Azure pour configurer la gestion des ressources à l’aide d’une liaison privée.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Créer une liaison privée de gestion des ressources

Lorsque vous créez une liaison privée de gestion des ressources, vous créez automatiquement l’association de liaison privée.

1. Dans le [portail](https://portal.azure.com), recherchez des **liaisons privées de gestion des ressources** et sélectionnez parmi les options disponibles.

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="Rechercher des liaisons privées de gestion des ressources":::

1. Si votre abonnement ne dispose pas déjà de liaisons privées de gestion des ressources, vous verrez une page vierge. Sélectionnez **Créer une liaison privée de gestion des ressources**.

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="Sélectionner pour créer des liaisons privées de gestion des ressources":::

1. Fournissez des valeurs pour la nouvelle liaison privée de gestion des ressources. Le groupe d’administration racine pour le répertoire que vous avez sélectionné est utilisé pour la nouvelle ressource. Sélectionnez **Revoir + créer**.

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="Spécifier des valeurs pour des liaisons privées de gestion des ressources":::

1. Une fois la validation réussie, sélectionnez **Créer**.

## <a name="create-private-endpoint"></a>Créer un point de terminaison privé

Créez maintenant un point de terminaison privé qui fait référence à la liaison privée de gestion des ressources.

1. Accédez au **Centre Private Link**. Sélectionnez **Créer un point de terminaison privé**.

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="Sélectionner Centre Private Link":::

1. Dans l’onglet **De base**, fournissez des valeurs pour votre point de terminaison privé.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="Renseigner les valeurs dans l’onglet De base":::

1. Dans l’onglet **Ressource**, sélectionnez **Se connecter à une ressource Azure dans mon annuaire**. Pour le type de ressource, sélectionnez **Microsoft.Authorization/resourceManagementPrivateLinks**. Pour la sous-ressource cible, sélectionnez **ResourceManagement**.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="Renseigner les valeurs pour la ressource":::

1. Dans l’onglet **configuration**, sélectionnez votre réseau virtuel. Nous vous recommandons d’intégrer à une zone DNS privée. Sélectionnez **Revoir + créer**.

1. Une fois la validation réussie, sélectionnez **Créer**.

## <a name="verify-private-dns-zone"></a>Vérifier la zone DNS privée

Pour vous assurer que votre environnement est correctement configuré, vérifiez l’adresse IP locale de la zone DNS.

1. Dans le groupe de ressources où vous avez déployé le point de terminaison privé, sélectionnez la ressource de zone DNS privée nommée **privatelink.azure.com**.

1. Vérifiez que le jeu d’enregistrements nommé **management** a une adresse IP locale valide.

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="Vérifier l’adresse IP locale":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les liaisons privées, consultez [Azure Private Link](../../private-link/index.yml).
