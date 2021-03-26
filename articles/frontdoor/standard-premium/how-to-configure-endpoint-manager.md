---
title: Configurer un point de terminaison Azure Front Door Standard/Premium avec Endpoint Manager
description: Cet article explique comment configurer un point de terminaison avec Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097653"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Configurer un point de terminaison Azure Front Door Standard/Premium (préversion) avec Endpoint Manager

> [!NOTE]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Consultez la **[documentation Azure Front Door](../front-door-overview.md)** .

Cet article vous montre comment créer un point de terminaison pour un profil Azure Front Door Standard/Premium existant avec Endpoint Manager.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir créer un point de terminaison Azure Front Door Standard/Premium avec Endpoint Manager, vous devez avoir créé au moins un profil Azure Front Door. Le profil doit avoir un ou plusieurs points de terminaison Azure Front Door Standard/Premium. Vous pouvez utiliser plusieurs profils pour organiser vos points de terminaison Azure Front Door Standard/Premium par domaine Internet, par application web ou selon d’autres critères. 

Pour créer un profil Azure Front Door, consultez [Créer un profil Azure Front Door Standard/Premium](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Créer un point de terminaison Azure Front Door Standard/Premium

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre profil Azure Front Door Standard/Premium.

1. Sélectionnez **Endpoint Manager**. Sélectionnez ensuite **Ajouter un point de terminaison** pour créer un point de terminaison.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Capture d’écran de l’ajout d’un point de terminaison via Endpoint Manager.":::

1. Dans la page **Ajouter un point de terminaison**, entrez, puis sélectionnez les paramètres suivants.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Capture d’écran de la page Ajouter un point de terminaison.":::

    | Paramètres | Valeur |
    | -------- | ----- |
    | Nom | Entrez un nom unique pour le nouveau point de terminaison Azure Front Door Standard/Premium. Ce nom est utilisé pour accéder à vos ressources mises en cache au niveau du domaine `<endpointname>.az01.azurefd.net`. |
    | Délai de réponse d’origine (secondes) | Entrez une valeur pour le délai d’expiration (en secondes) qu’Azure Front Door doit attendre avant de considérer que la connexion avec l’origine a expiré. |
    | Statut | Cochez la case pour activer ce point de terminaison. |

## <a name="add-domains-origin-group-routes-and-security"></a>Ajouter des domaines, un groupe d’origines, des routes et la sécurité

1. Sélectionnez **Modifier le point de terminaison** au niveau du point de terminaison pour configurer la route.

1. Dans la page **Modifier le point de terminaison**, sélectionnez **+ Ajouter** sous Domaines.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Capture d’écran de la sélection d’un domaine dans la page Modifier le point de terminaison.":::

### <a name="add-domain"></a>Ajouter un domaine

1. Dans la page **Ajouter un domaine**, choisissez d’associer un domaine *de votre profil Azure Front Door* ou d’*ajouter un nouveau domaine*. Pour plus d’informations sur la création d’un tout nouveau domaine, consultez [Créer un domaine personnalisé Azure Front Door Standard/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Capture d’écran de la page Ajouter un domaine.":::

1. Sélectionnez **Ajouter** pour ajouter le domaine au point de terminaison actif. Le domaine sélectionné doit apparaître dans le panneau Domaine.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Capture d’écran des domaines dans la vue des domaines.":::

### <a name="add-origin-group"></a>Ajouter un groupe d’origines

1. Sélectionnez **Ajouter** dans la vue Groupes d’origines. La page **Ajouter un groupe d’origines** s’affiche. 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Capture d’écran de la page Ajouter un groupe d’origines.":::

1. Pour **Nom**, entrez un nom unique pour le nouveau groupe d’origines.

1. Sélectionnez **Ajouter une origine** pour ajouter une nouvelle origine au groupe actif.
 
#### <a name="health-probes"></a>Sondes d’intégrité
Front Door envoie régulièrement des requêtes de sonde HTTP/HTTPS à chacune de vos origines. Les requêtes de sonde déterminent la proximité et l’intégrité de chaque origine, afin d’équilibrer la charge des requêtes de vos utilisateurs finaux. Les paramètres de sonde d’intégrité d’un groupe d’origines définissent la façon dont nous sondons l’état d’intégrité de l’origine d’une application. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

> [!WARNING]
> Dans la mesure où Front Door a de nombreux environnements de périphérie, le volume des sondes d’intégrité pour votre origine peut être assez grand, allant de 25 à 1 200 requêtes par minute, en fonction de la fréquence configurée pour la sonde d’intégrité. Avec la fréquence de sondage par défaut de 30 secondes, le volume des sondes sur votre origine devrait être d’environ 200 requêtes par minute.

* **État** : spécifiez si vous voulez activer la détection de l’intégrité. Si vous avez une seule origine dans votre groupe d’origines, vous pouvez choisir de désactiver les sondes d’intégrité afin de réduire la charge sur le back-end de votre application. Même si vous avez plusieurs origines dans le groupe mais qu’une seule est dans l’état Activé, vous pouvez désactiver les sondes d’intégrité.
   
* **Chemin** : URL utilisée pour les requêtes de sonde pour toutes les origines de ce groupe d’origines. Par exemple, si une de vos origines est contoso-westus.azurewebsites.net et que le chemin est défini sur /probe/test.aspx, les environnements Front Door, en supposant que le protocole est défini sur HTTP, envoient les requêtes de sondes d’intégrité à `http://contoso-westus.azurewebsites.net/probe/test.aspx`. 
   
* **Protocole** : définit si les requêtes de sonde d’intégrité de Front Door vers votre origine sont envoyées via le protocole HTTP ou HTTPS.
   
* **Méthode de sondage** : Méthode HTTP à utiliser pour l’envoi des sondes d’intégrité. Les options incluent GET ou HEAD (par défaut).

    > [!NOTE]
    > Afin de limiter la charge et les coûts sur votre origine, Front Door recommande d’utiliser des requêtes HEAD pour les sondes d’intégrité.

* **Intervalle (en secondes)**  : définit la fréquence des sondes d’intégrité vers votre origine, ou les intervalles selon lesquels chacun des environnements Front Door envoie une sonde.
   
    >[!NOTE]
    >Pour accélérer les basculements, baissez la valeur de l’intervalle. Plus la valeur est faible, plus le volume de sondes d’intégrité reçues par votre origine est grand. Par exemple, si l’intervalle est défini sur 30 secondes avec 100 points de présence (POP) Front Door dans le monde entier, chaque back-end reçoit environ 200 requêtes d’intégrité par minute.

#### <a name="load-balancing"></a>Équilibrage de la charge
Les paramètres d’équilibrage de la charge pour le groupe d’origines définissent comment nous évaluons les sondes d’intégrité. Ces paramètres déterminent si le serveur principal est sain ou pas. Ils vérifient également comment équilibrer la charge du trafic entre différentes origines du groupe d’origines. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

- **Taille d’échantillon** : Identifie le nombre d’échantillons de sondes d’intégrité nous devons prendre en compte pour évaluer l’intégrité de l’origine.

- **Taille d’échantillon réussi**. Définit la taille d’échantillon comme mentionné précédemment, le nombre d’échantillons réussis nécessaires pour déclarer que l’origine est saine. Par exemple, imaginons le cas d’un intervalle de sonde d’intégrité Front Door de 30 secondes, dont la taille d’échantillon est de 5, et la taille d’échantillon réussi de 3. Chaque fois que nous évaluons les sondes d’intégrité pour votre origine, nous examinons les 5 derniers échantillons sur 150 secondes (5 x 30). Au moins 3 sondages réussis sont requis pour déclarer que le back-end est sain.

- **Sensibilité de la latence (latence supplémentaire)** . Définit si vous souhaitez que Front Door envoie la requête à l’origine dans la plage de sensibilité de la mesure de la latence ou transfère la requête vers le back-end le plus proche.

Sélectionnez **Ajouter** pour ajouter le groupe d’origines au point de terminaison actuel. Le groupe d’origines doit apparaître dans le panneau Groupe d’origines.

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Capture d’écran d’origines dans le groupe d’origines.":::

### <a name="add-route"></a>Ajouter une route

Sélectionnez **Ajouter** dans la vue Routes : la page **Ajouter une route**  apparaît. Pour plus d’informations sur la façon d’associer le domaine et le groupe d’origines, consultez [Créer une route Azure Front Door](how-to-configure-route.md).

### <a name="add-security"></a>Ajouter la sécurité

1. Sélectionnez **Ajouter** dans la vue Sécurité : la page **Ajouter une stratégie WAF** apparaît.
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Capture d’écran de la page Ajouter une stratégie WAF.":::

1. **Stratégie WAF** : sélectionnez une stratégie WAF que vous voulez appliquer au domaine sélectionné au sein de ce point de terminaison. 
  
   Sélectionnez **Créer** pour créer une toute nouvelle stratégie WAF.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Capture d’écran de la création d’une nouvelle stratégie WAF.":::
   
    **Nom** : entrez un nom unique pour la nouvelle stratégie WAF. Vous pouvez modifier cette stratégie et affiner sa configuration à partir de la page Web Application Firewall.

    **Domaines** : sélectionnez le domaine auquel appliquer la stratégie WAF.

1. Sélectionnez le bouton **Ajouter**. La stratégie WAF doit apparaître dans le panneau Sécurité.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Capture d’écran de la stratégie WAF dans la vue Sécurité.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer un point de terminaison quand il n’est plus nécessaire, sélectionnez **Supprimer le point de terminaison** à la fin de la ligne du point de terminaison. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Capture d’écran montrant comment supprimer un point de terminaison.":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les domaines personnalisés, passez à [Ajout d’un domaine personnalisé](how-to-add-custom-domain.md).
