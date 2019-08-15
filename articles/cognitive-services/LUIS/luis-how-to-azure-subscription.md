---
title: Clés d’abonnement - LUIS
titleSuffix: Azure Cognitive Services
description: Vous n’avez pas besoin de créer de clés d’abonnement pour utiliser vos 1 000 premières requêtes de point de terminaison gratuites. Si vous recevez une erreur _hors quota_ sous la forme d’une erreur HTTP 403 ou 429, vous devez créer une clé et l’affecter à votre application.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932855"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Utilisation des clés d’abonnement avec votre application LUIS

Lorsque vous utilisez Language Understanding (LUIS) pour la première fois, vous n’avez pas besoin de créer de clés d’abonnement. 1 000 requêtes de point de terminaison vous sont données pour commencer. 

Pour des tests et des prototypes uniquement, utilisez le niveau gratuit (F0). Pour les systèmes de production, utilisez un niveau [payant](https://aka.ms/luis-price-tier). N’utilisez pas la [clé de création](luis-concept-keys.md#authoring-key) pour les requêtes de point de terminaison en production.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Créer la ressource de runtime de point de terminaison de prédiction dans le portail Azure

Vous créez la [ressource de point de terminaison de prédiction](get-started-portal-deploy-app.md#create-the-endpoint-resource) dans le portail Microsoft Azure. Cette ressource doit être utilisée seulement pour les requêtes de prédiction du point de terminaison. N’utilisez pas cette ressource pour apporter des modifications à l’application.

Vous pouvez créer une ressource Language Understanding ou une ressource Cognitive Services. Si vous créez une ressource Language Understanding, une bonne pratique consiste à ajouter le type de ressource au nom de la ressource. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>Utilisation de la ressource à partir du portail LUIS

Si vous utilisez la ressource à partir du portail LUIS, vous n’avez pas besoin de connaître votre clé et votre emplacement. Au lieu de cela, vous devez connaître le locataire de votre ressource, votre abonnement et le nom de votre ressource.

Une fois que vous avez [affecté](#assign-resource-key-to-luis-app-in-luis-portal) votre ressource à votre application LUIS dans le portail LUIS, la clé et l’emplacement sont fournis dans le cadre de l’URL du point de terminaison de prédiction de requête dans la page **Paramètres des clés et points de terminaison** de la section Gérer.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Utilisation de la ressource à partir de l’API ou du SDK REST

Si vous utilisez la ressource à partir de l’API ou du SDK REST, vous devez connaître votre clé et votre emplacement. Ces informations sont fournies dans le cadre de l’URL du point de terminaison de prédiction de requête dans la page **Paramètres des clés et points de terminaison** de la section Gérer ainsi que dans le portail Azure, dans les pages Vue d’ensemble et Clés de la ressource.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Affecter une clé de ressource à l’application LUIS dans le portail LUIS

Chaque fois que vous créez une ressource pour LUIS, vous devez [l’attribuer à l’application LUIS](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Une fois qu’elle est affectée, vous n’avez plus besoin d’effectuer cette étape, sauf si vous créez une nouvelle ressource. Vous pouvez être amené à créer une nouvelle ressource pour étendre les régions de votre application ou pour prendre en charge un nombre plus élevé de requêtes de prédiction.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Désaffecter la ressource
Quand vous désaffectez la clé de point de terminaison, elle n’est pas supprimée d’Azure. Elle n’est simplement plus liée à LUIS. 

Quand une clé de point de terminaison est désaffectée, ou non affectée à l’application, toute requête à l’URL de point de terminaison retourne une erreur : `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Inclure tous les scores d’intention prédits
La case à cocher **Include all predicted intent scores** (Inclure tous les scores d’intention prédits) permet de choisir que la réponse à la requête de point de terminaison inclue le score de prédiction pour chaque intention. 

Ce paramètre permet à votre chatbot ou à application appelante de LUIS de prendre une décision par programmation basée sur les scores des intentions renvoyées. En règle générale, les deux premières intentions sont les plus intéressantes. Si le score supérieur est l’intention None, votre chatbot peut choisir de poser une question de suivi qui effectue un choix définitif entre l’intention None et l’autre intention de score élevé. 

Les intentions et leurs scores sont également inclus dans les journaux d’activité de point de terminaison. Vous pouvez [exporter](luis-how-to-start-new-app.md#export-app) ces journaux d’activité et analyser les scores. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Activer le vérificateur orthographique Bing 
Dans les paramètres d’URL de point de terminaison (**Endpoint url settings**), le bouton bascule **Bing spell checker** (Vérificateur orthographique Bing) permet à LUIS de corriger les mots mal orthographiés avant la prédiction. Créez une **[clé de Vérification orthographique Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Ajoutez le paramètre de chaîne de requête **spellCheck=true** et **bing-spell-check-subscription-key={VOTRE_CLÉ_BING}** . Remplacez `{YOUR_BING_KEY_HERE}` par le clé de votre vérificateur orthographique Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Régions de publication

En savoir plus sur les[régions](luis-reference-regions.md) de publication, notamment la publication en [Europe](luis-reference-regions.md#publishing-to-europe) et en [Australie](luis-reference-regions.md#publishing-to-australia). Les régions de publication sont différentes des régions de création. Créez une application dans la région de création qui correspond à la région de publication souhaitée pour le point de terminaison de requête.

## <a name="assign-resource-without-luis-portal"></a>Affecter des ressources sans portail LUIS

À des fins d’automation (par exemple pour le pipeline CI/CD), vous souhaiterez peut-être automatiser l’affectation d’une ressource LUIS à une application LUIS. Pour ce faire, vous devez suivre les étapes ci-dessous :

1. Obtenir un jeton Azure Resource Manager à partir de ce [site web](https://resources.azure.com/api/token?plaintext=true). Ce jeton expire ; veillez donc à l’utiliser immédiatement. La requête retourne un jeton Azure Resource Manager.

    ![Demander un jeton Azure Resource Manager et recevoir un jeton Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Utiliser le jeton pour demander les ressources LUIS dans les abonnements, à partir de l’[API LUIS d’obtention de comptes Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auxquels votre compte d’utilisateur a accès. 

    Cette API POST nécessite les paramètres suivants :

    |En-tête|Valeur|
    |--|--|
    |`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.| 
    |`Ocp-Apim-Subscription-Key`|Votre [clé de création](luis-how-to-account-settings.md).|

    Cette API retourne un tableau d’objets JSON de vos abonnements LUIS, notamment l’ID d’abonnement, le groupe de ressources et le nom de la ressource, retourné en tant que nom de compte. Recherchez l’élément dans le tableau qui est la ressource LUIS à affecter à l’application LUIS. 

1. Affectez le jeton à la ressource LUIS avec l’API d’[affectation de compte LUIS Azure à une application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Cette API POST nécessite les paramètres suivants :

    |Type|Paramètre|Valeur|
    |--|--|--|
    |En-tête|`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |En-tête|`Ocp-Apim-Subscription-Key`|Votre [clé de création](luis-how-to-account-settings.md).|
    |En-tête|`Content-type`|`application/json`|
    |Querystring|`appid`|L’ID d’application LUIS. 
    |body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quand cette API réussit, elle retourne l’état 201 - créé. 

## <a name="change-pricing-tier"></a>Changer le niveau tarifaire

1.  Dans [Azure](https://portal.azure.com), recherchez votre abonnement LUIS. Sélectionnez l’abonnement LUIS.
    ![Rechercher votre abonnement LUIS](./media/luis-usage-tiers/find.png)
1.  Sélectionnez **Niveau tarifaire** pour afficher les niveaux tarifaires disponibles. 
    ![Afficher les niveaux tarifaires](./media/luis-usage-tiers/subscription.png)
1.  Sélectionnez le niveau tarifaire, puis choisissez **Sélectionner** pour enregistrer le changement apporté. 
    ![Modifier votre niveau de paiement LUIS](./media/luis-usage-tiers/plans.png)
1.  Lorsque la modification de tarification est terminée, une fenêtre contextuelle vérifie le nouveau niveau tarifaire. 
    ![Vérifier votre niveau de paiement LUIS](./media/luis-usage-tiers/updated.png)
1. N’oubliez pas d’[affecter cette clé de point de terminaison](#assign-endpoint-key) sur la page **Publier** et de l’utiliser dans toutes les requêtes de point de terminaison. 

## <a name="fix-http-status-code-403-and-429"></a>Corriger les codes d’état HTTP 403 et 429

Vous obtenez des codes d’état d’erreur 403 et 429 lorsque vous dépassez le nombre de transactions par seconde ou le nombre de transactions par mois pour votre niveau tarifaire.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Lorsque vous recevez un code d’état d’erreur HTTP 403

Lorsque vous utilisez l’ensemble des 1 000 requêtes de point de terminaison gratuites ou quand vous dépassez le quota de transactions mensuel de votre niveau tarifaire, vous recevez un code d’état d’erreur HTTP 403. 

Pour corriger cette erreur, vous devez [modifier votre niveau tarifaire](luis-how-to-azure-subscription.md#change-pricing-tier) pour en choisir un de niveau supérieur ou [créer une ressource](get-started-portal-deploy-app.md#create-the-endpoint-resource) et [l’attribuer à votre application](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Les solutions pour corriger cette erreur incluent :

* Dans le [portail Microsoft Azure](https://portal.azure.com), sur votre ressource Language Understanding, dans **Gestion des ressources -> Niveau tarifaire**, remplacez votre niveau tarifaire par un niveau TPS supérieur. Vous n’avez aucune action à effectuer dans le portail Language Understanding si votre ressource est déjà attribuée à votre application Language Understanding.
*  Si votre utilisation dépasse le niveau tarifaire le plus élevé, ajoutez plus de ressources Language Understanding avec un équilibreur de charge placé devant celles-ci. Le [conteneur Language Understanding](luis-container-howto.md) avec Kubernetes ou Docker Compose peut vous y aider.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Lorsque vous recevez un code d’état d’erreur HTTP 429

Ce code d’état est renvoyé lorsque le nombre de transactions par seconde dépasse votre niveau tarifaire.  

Les solutions pour corriger le problème incluent :

* Vous pouvez [augmenter votre niveau tarifaire](#change-pricing-tier), si vous n’êtes pas au niveau le plus élevé.
* Si votre utilisation dépasse le niveau tarifaire le plus élevé, ajoutez plus de ressources Language Understanding avec un équilibreur de charge placé devant celles-ci. Le [conteneur Language Understanding](luis-container-howto.md) avec Kubernetes ou Docker Compose peut vous y aider.
* Vous pouvez réguler vos requêtes d’application cliente avec une [stratégie de nouvelles tentatives](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que vous implémentez lorsque vous recevez ce code d’état. 

## <a name="viewing-summary-usage"></a>Affichage résumé de l’utilisation
Vous pouvez afficher des informations sur l’utilisation de LUIS dans Azure. La page **Vue d’ensemble** affiche des informations récapitulatives récentes, y compris les appels et les erreurs. Si vous effectuez une requête de point de terminaison LUIS, puis consultez immédiatement la **page Vue d’ensemble**, attendez cinq minutes, le temps que l’utilisation s’affiche.

![Affichage résumé de l’utilisation](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personnalisation des graphiques d’utilisation
Les métriques fournissent une vue plus détaillée des données.

![Mesures par défaut](./media/luis-usage-tiers/metrics-default.png)

Vous pouvez configurer vos graphiques de métriques pour la période et le type de métrique. 

![Mesures personnalisées](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerte de seuil de nombre total de transactions
Si vous souhaitez connaître le moment où vous avez atteint un certain seuil de transactions (10 000 transactions, par exemple), vous pouvez créer une alerte. 

![Alertes par défaut](./media/luis-usage-tiers/alert-default.png)

Ajout d’une alerte de métrique pour le **nombre total d’appels** pendant une certaine période. Ajouter les adresses de messagerie de toutes les personnes qui doivent recevoir l’alerte. Ajoutez des webhooks pour tous les systèmes qui doivent recevoir l’alerte. Vous pouvez également exécuter une application logique lorsque l’alerte est déclenchée. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser des [versions](luis-how-to-manage-versions.md) pour gérer les modifications apportées à votre application LUIS.
