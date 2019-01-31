---
title: Clés d’abonnement
titleSuffix: Language Understadning - Azure Cognitive Services
description: 'LUIS utilise deux clés : la clé de création gratuite pour créer votre modèle et la clé de point de terminaison limitée pour interroger le point de terminaison de prédiction avec des énoncés de l’utilisateur.'
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 81744e2af75410da911ee17f2c4acd05d5a57051
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222591"
---
# <a name="authoring-and-query-prediction-endpoint-keys-in-luis"></a>Clés de point de terminaison de création et de prédiction de requête dans LUIS
LUIS utilise deux clés : [création](#programmatic-key) et [point de terminaison](#endpoint-key). La clé de création est générée automatiquement lorsque vous créez votre compte LUIS. Lorsque vous êtes prêt à publier votre application LUIS, vous devez [créer la clé de point de terminaison](luis-how-to-azure-subscription.md), [l’affecter](luis-how-to-azure-subscription.md) à votre application LUIS et [l’utiliser avec la requête de point de terminaison](#use-endpoint-key-in-query). 

|Clé|Objectif|
|--|--|
|[Clé de création](#programmatic-key)|Création, publication, gestion des collaborateurs, contrôle de version|
|[Clé de point de terminaison](#endpoint-key)| Interrogation|

Il est important de créer des applications LUIS dans des [régions](luis-reference-regions.md#publishing-regions) où vous voulez également publier et interroger.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Clé de création

Une clé de création, également connue sous le nom de clé de démarrage, est générée automatiquement lorsque vous créez un compte LUIS et elle est gratuite. Vous avez une clé de création pour toutes vos applications LUIS pour chaque [région](luis-reference-regions.md) de création. La clé de création est fournie pour créer votre application LUIS, ou pour tester les requêtes de point de terminaison. 

Pour rechercher la clé de création, connectez-vous à [LUIS](luis-reference-regions.md#luis-website), puis cliquez dans la barre de navigation en haut à droite sur le nom du compte pour ouvrir les **Paramètres du compte**.

![clé de création](./media/luis-concept-keys/programatic-key.png)

Quand vous souhaitez faire des **requêtes de point de terminaison de production**, créez l’[abonnement LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) Azure. 

> [!CAUTION]
> Pour des raisons pratiques, de nombreux exemples utilisent la clé de création, car son [quota](luis-boundaries.md#key-limits) inclut quelques appels de point de terminaison.  

## <a name="endpoint-key"></a>Clé de point de terminaison
Une fois que vous aurez besoin de **requêtes de point de terminaison de production**, créez une ressource Azure, puis attribuez-la à l’application LUIS. 

[!INCLUDE [Azure resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

À la fin du processus de création de ressource Azure, [affectez la clé](luis-how-to-azure-subscription.md) à l’application. 

    * La clé de point de terminaison permet un quota d’accès au point de terminaison basé sur le plan de l’utilisation que vous avez spécifié lors de la création de la clé. Consultez [tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) pour des informations sur la tarification.

    * La clé de point de terminaison peut être utilisée pour toutes vos applications LUIS, ou pour des applications LUIS spécifiques. 

    * N’utilisez pas la clé de point de terminaison pour la création d’applications LUIS. 

## <a name="use-endpoint-key-in-query"></a>Utilisez la clé de point de terminaison dans la requête
Le point de terminaison LUIS accepte deux styles de requêtes, les deux utilisant la clé de point de terminaison, mais à des emplacements différents :

|Verbe|Exemple d’url et emplacement de la clé|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>valeur de chaîne de requête pour `subscription-key`<br><br>Modifiez la valeur de requête de votre point de terminaison de la `subscription-key` de la clé de création (démarrage) à la nouvelle clé du point de terminaison afin d’utiliser le quota de clé de terminaison de LUIS. Si vous créez la clé et si vous l’affectez, mais que vous ne modifiez pas la valeur de requête de point de terminaison pour subscription-key, vous n’utilisez pas votre quota de clé de point de terminaison.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> valeur de l’en-tête pour `Ocp-Apim-Subscription-Key`<br><br>Modifiez la valeur de requête de votre point de terminaison de la `Ocp-Apim-Subscription-Key` de la clé de création (démarrage) à la nouvelle clé du point de terminaison afin d’utiliser le quota de clé de terminaison de LUIS. Si vous créez la clé et si vous l’affectez, mais que vous ne modifiez pas la valeur de requête de point de terminaison pour `Ocp-Apim-Subscription-Key`, vous n’utilisez pas votre quota de clé de point de terminaison.|

L’ID d’application utilisé dans les URL précédentes, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, est l’application IoT publique utilisée pour la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Utilisation de l’API par Ocp-Apim-Subscription-Key
Les API LUIS utilisent l’en-tête, `Ocp-Apim-Subscription-Key`. Le nom de l’en-tête ne change pas en fonction de la clé et de l’ensemble d’API que vous utilisez. Définissez l’en-tête sur la clé de création pour les API de création. Si vous utilisez le point de terminaison, définissez l’en-tête sur la clé du point de terminaison. 

Vous ne pouvez pas utiliser la clé de point de terminaison pour créer des API. Si vous le faites, vous recevez une erreur 401 : accès refusé en raison d’une clé de point de terminaison non valide. 

## <a name="key-limits"></a>Limites de clés
Consultez [Limites de clé](luis-boundaries.md#key-limits) et [Régions Azure](luis-reference-regions.md). La clé de création est gratuite et utilisée pour la création. La clé de point de terminaison LUIS a un niveau gratuit, mais doit être créée par vos soins et associée à votre application LUIS sur la page **Publier**. Elle ne peut pas être utilisée pour la création, mais seulement pour les requêtes de point de terminaison.

Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication souhaitée.

## <a name="key-limit-errors"></a>Erreurs de limites de clés
Si vous dépassez votre quota par seconde, vous recevez une erreur HTTP 429. Si vous dépassez votre quota mensuel, vous recevez une erreur HTTP 403. Corrigez ces erreurs en obtenant une clé de [point de terminaison](#endpoint-key) LUIS, en [affectant](luis-how-to-azure-subscription.md) la clé à l’application sur la page **Publier** du site web [LUIS](luis-reference-regions.md#luis-website).

## <a name="assignment-of-the-endpoint-key"></a>Affectation de la clé de point de terminaison

Vous pouvez [affecter](luis-how-to-azure-subscription.md) la clé de point de terminaison dans le [portail LUIS](https://www.luis.ai) ou via les API correspondantes. 


## <a name="next-steps"></a>Étapes suivantes

* Découvrir les [principes](luis-how-to-azure-subscription.md) des clés de création et de point de terminaison.
