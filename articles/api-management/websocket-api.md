---
title: Importer une API WebSocket à l’aide du portail Azure | Microsoft Docs
titleSuffix: ''
description: Découvrez comment API Management prend en charge WebSocket, ajouter une API WebSocket et les limitations de WebSocket.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 11/2/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 027a87a7502f551b7fb97d52a732a90bc0b8aa45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065681"
---
# <a name="import-a-websocket-api"></a>Importer une API WebSocket

Avec la solution API WebSocket d’API Management, vous pouvez désormais gérer, protéger, observer et exposer des API WebSocket et des API REST avec gestion des API et fournir un hub central pour la découverte et l’utilisation de toutes les API. Les éditeurs d’API peuvent rapidement ajouter une API WebSocket dans API Management via :
* Un mouvement simple dans le Portail Azure, et 
* API Management et Azure Resource Manager. 

Vous pouvez sécuriser les API WebSocket en appliquant des stratégies de contrôle d’accès existantes, telles que la [validation JWT](./api-management-access-restriction-policies.md#ValidateJWT). Vous pouvez également tester les API WebSocket à l’aide des consoles de test de l’API dans le Portail Azure et le portail des développeurs. En se basant sur les capacités d’observation existantes, API Management fournit des indicateurs de performance et des journaux pour la surveillance et le dépannage des API WebSocket. 

En lisant cet article, vous pourrez :
> [!div class="checklist"]
> * Comprendre le flux direct du WebSocket.
> * Ajouter une API WebSocket dans votre instance API Management.
> * Tester votre API WebSocket.
> * Consulter les métriques et les journaux de votre API WebSocket.
> * Découvrez les limitations de l’API WebSocket.

## <a name="prerequisites"></a>Prérequis

- Disposer d’une instance d’API Management. Si vous ne l’avez pas déjà fait, [créez-en un](get-started-create-service-instance.md).
- Une API WebSocket. 

## <a name="websocket-passthrough"></a>Relais WebSocket

API Management prend en charge le relais WebSocket. 

:::image type="content" source="./media/websocket-api/websocket-api-passthrough.png" alt-text="Illustration visuelle du flux de relais WebSocket":::

Pendant le transfert WebSocket, l’application cliente établit une connexion WebSocket avec la passerelle API Management, qui établit ensuite une connexion avec les services principaux correspondants. API Management transmet ensuite les messages client-serveur WebSocket.

1. L’application cliente envoie une requête d’établissement de liaison WebSocket à la passerelle APIM, en appelant l’opération onHandshake.
1. La passerelle APIM envoie une requête d’établissement d'une liaison WebSocket au service principal correspondant.
1. Le service principal met à niveau une connexion à WebSocket.
1. La passerelle APIM met à niveau la connexion correspondante à WebSocket.
1. Une fois la paire de connexions établie, APIM répartira les messages entre l’application cliente et le service principal.
1. L’application cliente envoie un message à la passerelle APIM.
1. La passerelle APIM transfère le message au service principal.
1. Le service principal envoie un message à la passerelle APIM.
1. La passerelle APIM transfère le message à l’application cliente.
1. Lorsque l’une ou l’autre se déconnecte, APIM met fin à la connexion correspondante.

> [!NOTE]
> Les connexions côté client et côté serveur se composent d’un mappage un-à-un. 

## <a name="onhandshake-operation"></a>Opération onHandshake

Selon le [protocole WebSocket](https://tools.ietf.org/html/rfc6455), lorsqu’une application cliente essaie d’établir une connexion WebSocket avec un service principal, elle envoie d’abord une [requête d’établissement d'une liaison d’ouverture](https://tools.ietf.org/html/rfc6455#page-6). Chaque API WebSocket dans API Management a une opération onHandshake. onHandshake est une opération système non modifiable, non amovible, créée automatiquement. L’opération onHandshake permet aux éditeurs d’API d’intercepter ces requêtes d’établissement d'une liaison et d’appliquer des stratégies d’API Management à celles-ci.

:::image type="content" source="./media/websocket-api/onhandshake-screen.png" alt-text="Exemple d’écran onHandshake":::

## <a name="add-a-websocket-api"></a>Ajouter une API WebSocket

1. Accédez à votre instance API Management.
1. Dans le menu de navigation latéral, sous la section **API**, sélectionnez **API**.
1. Sous **Définir une nouvelle API**, sélectionnez l’icône **WebSocket**.
1. Dans la boîte de dialogue, sélectionnez **Complète** et renseignez les champs de formulaire requis.

    | Champ | Description |
    |----------------|-------|
    | Nom complet | Le nom par lequel votre API WebSocket sera affichée. |
    | Nom | Nom brut de l’API WebSocket. Se remplit automatiquement à mesure que vous tapez le nom complet. |
    | URL WebSocket | URL de base avec le nom de votre WebSocket. Par exemple : *ws://example.com/your-socket-name* |
    | Modèle d’URL | Accepter la valeur par défaut |
    | Suffixe de l’URL de l’API| Ajoutez un suffixe d’URL pour identifier cette API spécifique dans cette instance de gestion des API. Il doit être unique dans cette instance APIM. |
    | Produits | Associez votre API WebSocket à un produit pour la publier. |
    | Passerelles | Associez votre API WebSocket à des passerelles existantes. |
 
1. Cliquez sur **Créer**.

## <a name="test-your-websocket-api"></a>Tester votre API WebSocket.

1. Accédez à votre API WebSocket.
1. Dans votre API WebSocket, sélectionnez l'opération onHandshake.
1. Sélectionnez l'onglet **Test** pour accéder à la console de test. 
1. Si vous le souhaitez, fournissez les paramètres de chaîne de requête requis pour l'établissement d'une liaison WebSocket.

    :::image type="content" source="./media/websocket-api/test-websocket-api.png" alt-text="Exemple d'API de test":::

1. Cliquez sur **Connecter**.
1. Consultez l'état de la connexion dans **Sortie**.
1. Entrez la valeur dans **Charge utile**. 
1. Cliquez sur **Envoyer**.
1. Consultez les messages reçus dans **Sortie**.
1. Répétez les étapes précédentes pour tester différentes charges utiles.
1. Une fois le test terminé, sélectionnez **Déconnecter**.

## <a name="view-metrics-and-logs"></a>Afficher les métriques et les journaux

Utilisez les fonctionnalités API Management et Azure Monitor standard pour [surveiller](api-management-howto-use-azure-monitor.md) les API WebSocket :

* Afficher des métriques d’API dans Azure Monitor
* Vous pouvez éventuellement activer les paramètres de diagnostic pour collecter et afficher les journaux de la passerelle API Management, y compris les opérations de l’API WebSocket

Par exemple, la capture d’écran suivante montre les réponses récentes de l’API WebSocket avec le `101` code de la table **ApiManagementGatewayLogs**. Ces résultats indiquent la réussite du basculement des demandes de TCP vers le protocole WebSocket.

:::image type="content" source="./media/websocket-api/query-gateway-logs.png" alt-text="Journaux de requêtes pour les requêtes de l’API WebSocket":::

## <a name="limitations"></a>Limites

Voici les restrictions actuelles de la prise en charge de WebSocket dans API Management :

* Les API WebSocket ne sont pas encore prises en charge dans le niveau Consommation.
* Les API WebSocket ne sont pas encore prises en charge dans la [passerelle auto-hébergée](./self-hosted-gateway-overview.md).
* Azure CLI, PowerShell et le SDK ne prennent pas en charge les opérations de gestion des API WebSocket.

### <a name="unsupported-policies"></a>Stratégies non prises en charge

Les stratégies suivantes ne sont pas prises en charge par et ne peuvent pas être appliquées à l’opération onHandshake :
*  Réponse factice
* Obtention à partir du cache
* Stockage dans le cache
* Autorisation des appels inter-domaines
* CORS
* JSONP
*  Set request method
* Définir le corps
* Conversion de XML à JSON
* Convert JSON to XML
* Transformation de XML à l’aide de XSLT
* Valider le contenu
* Valider les paramètres
* Valider les en-têtes
* Valider le code d’état

> [!NOTE]
> Si vous avez appliqué les stratégies aux étendues supérieures (par exemple, générale ou produit) et qu’elles ont été héritées par une API WebSocket via la stratégie, elles seront ignorées au moment de l’exécution.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
