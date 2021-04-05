---
title: Configurer une route Azure Front Door
description: Cet article explique comment configurer une route entre vos domaines et des groupes d’origines.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097631"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Configurer une route Azure Front Door Standard/Premium

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article explique chacun des paramètres utilisés pour créer une route AFD (Azure Front Door) pour un point de terminaison existant. Après avoir ajouté un domaine personnalisé et une origine à votre point de terminaison Azure Front Door existant, vous devez configurer une route pour définir l’association entre domaines et origines afin de router le trafic entre eux.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer une route Azure Front Door, vous devez créer au moins un groupe d’origines et un domaine personnalisé au sein du point de terminaison actuel. 

Pour configurer un groupe d’origines, consultez [Configurer une origine Azure Front Door Standard/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Créer une route Azure Front Door Standard/Premium

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre profil Azure Front Door Standard/Premium.

1. Sélectionnez **Endpoint Manager** sous **Paramètres**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Capture d’écran des paramètres d’Endpoint Manager pour Front Door." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Sélectionnez ensuite **Modifier le point de terminaison** en regard du point de terminaison pour lequel vous souhaitez configurer la route.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Capture d’écran montrant la sélection de l’option Modifier le point de terminaison.":::

1. La page **Modifier le point de terminaison** s’affiche. Sélectionnez **+ Ajouter** pour les routes.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Capture d’écran montrant l’option d’ajout de route dans la page Modifier le point de terminaison.":::    
    
1. Dans la page **Ajouter une route**, entrez ou sélectionnez les informations suivantes.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Capture d’écran de la page Ajouter une route." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Paramètre | Valeur |
    | --- | --- |
    | Nom | Entrez un nom unique pour la nouvelle route. |   
    | Domain| Sélectionnez un ou plusieurs domaines qui ont été validés et qui ne sont pas associés à une autre route. |
    | Modèles à mettre en correspondance  | Configurez tous les modèles de chemin d’URL que cette route acceptera. Par exemple, vous pouvez définir ce paramètre sur `/images/*` pour accepter toutes les requêtes sur l’URL `www.contoso.com/images/*`. AFD essaiera d’abord de déterminer le trafic en fonction d’une correspondance exacte, puis, si aucun chemin ne correspond exactement, recherchera un chemin générique qui correspond. Si aucune règle de routage n’est trouvée avec un chemin correspondant, la demande est rejetée et une réponse HTTP 400 : Demande incorrecte est retournée. |
    | Protocoles acceptés | Spécifiez les protocoles qu’Azure Front Door doit accepter quand le client effectue la requête. |
    | Rediriger | Spécifier si le protocole HTTPS est appliqué à la requête entrante avec une requête HTTP |
    | Groupe d’origines | Sélectionnez le groupe d’origines de destination du transfert quand la requête de retour à l’origine est effectuée. |
    | Chemin de l’origine | Entrez le chemin vers les ressources que vous souhaitez mettre en cache. Pour permettre la mise en cache de n’importe quelle ressource au niveau du domaine, laissez ce paramètre vide. |
    | Protocole de transfert | Sélectionnez le protocole utilisé pour transférer la requête. |
    | Mise en cache | Sélectionnez cette option pour activer la mise en cache du contenu statique avec Azure Front Door. |
    | Règle | Sélectionnez les ensembles de règles qui seront appliqués à cet itinéraire. Pour plus d’informations sur la configuration des règles, consultez [Configurer un ensemble de règles pour Azure Front Door](how-to-configure-rule-set.md) | 

1. Sélectionnez **Ajouter** pour créer la route. La route s’affiche dans la liste des routes pour le point de terminaison.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Capture d’écran de la liste des routes.":::  
    
## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer une route quand vous n’en avez plus besoin, sélectionnez-la, puis sélectionnez **Supprimer**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Capture d’écran montrant comment supprimer une route.":::  

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les domaines personnalisés, passez au tutoriel consacré à l’ajout d’un domaine personnalisé à votre point de terminaison Azure Front Door.

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé]()
