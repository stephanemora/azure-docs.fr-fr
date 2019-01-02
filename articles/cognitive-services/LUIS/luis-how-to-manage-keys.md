---
title: Gérer les clés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Après avoir créé une clé de point de terminaison LUIS dans le portail Azure, affectez la clé à l’application LUIS et obtenez l’URL de point de terminaison correcte. Utilisez cette URL de point de terminaison pour obtenir des prédictions LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088100"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Ajouter une ressource LUIS Azure à l’application

Après avoir créé une ressource LUIS dans le portail Azure, affectez la ressource à l’application LUIS et obtenez l’URL de point de terminaison correcte. Utilisez cette URL de point de terminaison pour obtenir des prédictions LUIS.

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


## <a name="assign-resource-in-luis-portal"></a>Affecter des ressources dans le portail LUIS

1. Créez une clé LUIS sur le [portail Azure](https://portal.azure.com). Pour plus d’instructions, consultez [Création d’une clé de point de terminaison à l’aide d’Azure](luis-how-to-azure-subscription.md).
 
2. Sélectionnez **Manage** (Gérer) dans le menu en haut à droite, puis sélectionnez **Keys and endpoints** (Clés et points de terminaison).

    [ ![Page Keys and endpoints](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Pour ajouter la clé LUIS, sélectionnez **Assign Resource +** (Affecter une ressource).

    ![Affecter une ressource à votre application](./media/luis-manage-keys/assign-key.png)

4. Sélectionnez un locataire dans la boîte de dialogue associée à l’adresse e-mail avec laquelle vous vous connectez au site web de LUIS.  

5. Choisissez le **Nom de l’abonnement** associé à la ressource Azure que vous souhaitez ajouter.

6. Sélectionnez le **Nom de la ressource LUIS**. 

7. Sélectionnez **Assign resource** (Affecter une ressource). 

8. Recherchez la nouvelle ligne dans le tableau et copiez l’URL de point de terminaison. Elle est correctement construite pour effectuer une requête HTTP GET au point de terminaison LUIS afin d’obtenir une prédiction. 

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

Les intentions et leurs scores sont également inclus dans les journaux de point de terminaison. Vous pouvez [exporter](luis-how-to-start-new-app.md#export-app) ces journaux et analyser les scores. 

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
Dans les paramètres d’URL de point de terminaison (**Endpoint url settings**), le bouton bascule **Bing spell checker** (Vérificateur orthographique Bing) permet à LUIS de corriger les mots mal orthographiés avant la prédiction. Créez une **[clé de Vérification orthographique Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

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

À des fins d’automation (par exemple pour le pipeline CI/CD), vous souhaiterez peut-être automatiser l’affectation d’une ressource LUIS à une application LUIS. Pour cela, vous devez effectuer les étapes suivantes :

1. Obtenir un jeton Azure Resource Manager à partir de ce [site web](https://resources.azure.com/api/token?plaintext=true). Ce jeton expire ; veillez donc à l’utiliser immédiatement. La requête retourne un jeton Azure Resource Manager.

    ![Demander un jeton Azure Resource Manager et recevoir le jeton Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Utiliser le jeton pour demander les ressources LUIS dans les abonnements, à partir de l’[API LUIS d’obtention de comptes Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auxquels votre compte d’utilisateur a accès. 

    Cette API POST nécessite les paramètres suivants :

    |En-tête|Valeur|
    |--|--|
    |`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.| 
    |`Ocp-Apim-Subscription-Key`|Votre [clé de création](luis-how-to-account-settings.md).|

    Cette API retourne un tableau d’objets JSON de vos abonnements LUIS, notamment l’ID d’abonnement, le groupe de ressources et le nom de la ressource, retourné en tant que nom de compte. Recherchez l’élément dans le tableau qui est la ressource LUIS à affecter à l’application LUIS. 

1. Affectez le jeton à la ressource LUIS avec l’API d’[affectation de compte LUIS Azure à une application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Cette API POST nécessite les paramètres suivants :

    |type|Paramètre|Valeur|
    |--|--|--|
    |En-tête|`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |En-tête|`Ocp-Apim-Subscription-Key`|Votre [clé de création](luis-how-to-account-settings.md).|
    |En-tête|`Content-type`|`application/json`|
    |Querystring|`appid`|L’ID d’application LUIS. 
    |body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quand cette API réussit, elle retourne l’état 201 - créé. 

## <a name="next-steps"></a>Étapes suivantes

Utilisez votre clé pour publier votre application sur la page **Publier votre application**. Pour obtenir des instructions sur la publication, consultez [Publier application](luis-how-to-publish-app.md).

Consultez [Clés dans LUIS](luis-concept-keys.md) pour comprendre les principes de création et de clé de point de terminaison de LUIS.
