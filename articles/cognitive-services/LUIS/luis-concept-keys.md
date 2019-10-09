---
title: Clés de création et d'exécution - LUIS
titleSuffix: Azure Cognitive Services
description: "LUIS utilise deux clés : la clé de création pour créer votre modèle et la clé d'exécution pour interroger le point de terminaison de prédiction avec des énoncés utilisateur."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703178"
---
# <a name="authoring-and-runtime-keys"></a>Clés de création et d'exécution


>[!NOTE]
>Avant de continuer, veuillez [migrer](luis-migration-authoring.md) les applications qui n'utilisent pas la ressource de création Azure.

LUIS utilise deux types de ressources Azure, chaque type étant doté de clés : 
 
* [Création](#programmatic-key) pour créer des intentions, des entités et des énoncés d'étiquettes, ainsi que pour former et publier. Lorsque vous êtes prêt à publier votre application LUIS, vous devez lui attribuer une [clé de point de terminaison de prédiction pour l'exécution](luis-how-to-azure-subscription.md).
* [Clé de point de terminaison de prédiction pour l'exécution](#prediction-endpoint-runtime-key). Les applications clientes, comme les bots conversationnels, doivent avoir accès au **point de terminaison de prédiction de la requête** du runtime via cette clé. 

|Clé|Objectif|Service cognitif `kind`|Service cognitif `type`|
|--|--|--|--|
|[Clé de création](#programmatic-key)|Création, formation, publication et test.|`LUIS.Authoring`|`Cognitive Services`|
|[Clé d'exécution du point de terminaison de prédiction](#prediction-endpoint-runtime-key)| Exécution du point de terminaison de prédiction de la requête avec un énoncé utilisateur pour déterminer les intentions et les entités.|`LUIS`|`Cognitive Services`|

LUIS fournit également une [clé de démarrage](luis-how-to-azure-subscription.md#starter-key) avec un quota de point de terminaison de prédiction de 1 000 transactions par mois. 

Pour vous faciliter la tâche, n'hésitez pas à créer les deux clés en même temps, même si cela n'est pas obligatoire.

Il est important de créer des applications LUIS dans les [régions](luis-reference-regions.md#publishing-regions) où vous souhaitez publier et interroger.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Clé de création

Une clé de création est automatiquement et gratuitement générée lorsque vous créez un compte LUIS. Lorsque vous commencez à utiliser LUIS, vous disposez d'une clé de démarrage adaptée à toutes vos applications LUIS pour chaque [région](luis-reference-regions.md) de création. La clé de création est destinée à vous fournir une authentification pour gérer votre application LUIS ou tester les requêtes du point de terminaison de prédiction. 

La génération de clés de création sur le portail Azure vous permet de contrôler les autorisations d'accès à la ressource de création en attribuant le [rôle de contributeur](#contributions-from-other-authors) à des utilisateurs. Vous devez disposer d’une autorisation au niveau de l’abonnement Azure pour ajouter des contributeurs. 

Pour rechercher la clé de création, connectez-vous à [LUIS](luis-reference-regions.md#luis-website), puis cliquez dans la barre de navigation en haut à droite sur le nom du compte pour ouvrir les **Paramètres du compte**.

![clé de création](./media/luis-concept-keys/authoring-key.png)

Si vous souhaitez effectuer des **requêtes d'exécution**, créez la [ressource LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) Azure. 

> [!CAUTION]
> Pour des raisons pratiques, de nombreux exemples utilisent la [clé de démarrage](#starter-prediction-endpoint-runtime-key) car son [quota](luis-boundaries.md#key-limits) inclut quelques appels gratuits au point de terminaison de prédiction.  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Clé d'exécution du point de terminaison de prédiction 

Si vous avez besoin de **requêtes de point de terminaison d'exécution**, créez une ressource LUIS (Language Understanding), puis attribuez-la à l'application LUIS. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

À la fin du processus de création de ressource, [attribuez la clé](luis-how-to-azure-subscription.md) à l'application. 

* La clé d'exécution (du point de terminaison de prédiction de la requête) permet un quota d'accès au point de terminaison basé sur le plan d'utilisation que vous avez spécifié lors de la création de la clé. Consultez [tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) pour des informations sur la tarification.

* La clé d'exécution peut être utilisée pour toutes vos applications LUIS, ou pour des applications LUIS spécifiques. 
* N'utilisez pas la clé d'exécution pour la création d'applications LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Clé d'exécution de point de terminaison de prédiction de démarrage

La clé de point de terminaison de prédiction **de démarrage** est fournie gratuitement et comprend 1 000 requêtes de point de terminaison de prédiction. Une fois ces requêtes utilisées, vous devez créer votre propre ressource de point de terminaison de prédiction pour Language Understanding.  

Il s'agit d'une ressource spéciale créée pour vous. Elle n'apparaît pas dans votre liste de ressources Azure car il s'agit d'une clé de démarrage temporaire. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Utiliser la clé d'exécution dans la requête
Le point de terminaison d'exécution LUIS accepte deux styles de requêtes, les deux utilisant la clé d'exécution de point de terminaison de prédiction, mais à des emplacements différents.

Le point de terminaison utilisé pour accéder à l'environnement d'exécution utilise un sous-domaine propre à la région de votre ressource, indiquée par le terme `{region}` dans le tableau suivant. 


#### <a name="v2-prediction-endpointtabv2"></a>[Point de terminaison de prédiction V2](#tab/V2)

|Verbe|Exemple d’url et emplacement de la clé|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[Point de terminaison de prédiction V3](#tab/V3)

|Verbe|Exemple d’url et emplacement de la clé|
|--|--|
|[GET](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[POST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

Apprenez-en davantage sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * * 

**GET** : Modifiez la valeur de requête de votre point de terminaison de la `runtime-key` de la clé de création (démarrage) à la nouvelle clé du point de terminaison afin d’utiliser le quota de clé de terminaison de LUIS. Si vous créez la clé et si vous l’affectez, mais que vous ne modifiez pas la valeur de requête de point de terminaison pour `runtime-key`, vous n’utilisez pas votre quota de clé de point de terminaison.

**POST** : Modifier la valeur d’en-tête pour `Ocp-Apim-Subscription-Key`<br>Si vous créez la clé d'exécution et attribuez celle-ci sans modifier la valeur de requête de point de terminaison de `Ocp-Apim-Subscription-Key`, vous n'utilisez pas votre clé d'exécution.

L’ID d’application utilisé dans les URL précédentes, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, est l’application IoT publique utilisée pour la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Attribution de la clé d'exécution

Vous pouvez [attribuer](luis-how-to-azure-subscription.md) la clé d'exécution à partir du [portail LUIS](https://www.luis.ai) ou via les API correspondantes. 

## <a name="key-limits"></a>Limites de clés

Vous pouvez générer jusqu'à 10 clés de création par région et par abonnement. 

Consultez [Limites de clés](luis-boundaries.md#key-limits) et [Régions Azure](luis-reference-regions.md). 

Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication dans laquelle vous souhaitez que votre application cliente soit située.

## <a name="key-limit-errors"></a>Erreurs de limites de clés
Si vous dépassez votre quota de transactions par seconde (TPS), vous recevez une erreur HTTP 429. Si vous dépassez votre quota de transactions par mois (TPM), vous recevez une erreur HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contributions d'autres auteurs



La gestion des contributions des collaborateurs dépend de l'état actuel de l'application.

**Pour les applications [migrées avec ressources de création](luis-migration-authoring.md)**  : les _contributeurs_ sont gérées sur le portail Azure pour la ressource de création, via la page **Contrôle d'accès (IAM)** . Apprenez à [ajouter un utilisateur](luis-how-to-collaborate.md) à l'aide de l'adresse e-mail du collaborateur et du rôle de _contributeur_. 

**Pour les applications qui n'ont pas encore été migrées** : tous les _collaborateurs_ sont gérés sur le portail Luis à partir de la page **Gérer -> Collaborateurs**.

### <a name="contributor-roles-vs-entity-roles"></a>Rôles de contributeur et rôles d'entité

Les [rôles d’entités](luis-concept-roles.md) s’appliquent au modèle de données de l’application LUIS. Les rôles de collaborateur/contributeur s'appliquent aux niveaux d'accès de création. 

## <a name="moving-or-changing-ownership"></a>Déplacement ou changement de propriétaire

Une application est définie par ses ressources Azure, qui sont elles-mêmes déterminées par l'abonnement du propriétaire. 

Vous pouvez déplacer votre application LUIS. Utilisez les ressources documentaires suivantes à partir du portail Azure ou de l'interface Azure CLI :

* [Déplacer l'application entre des ressources de création LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/resource-group-move-resources.md)
* [Déplacer des ressources au sein d'un même abonnement ou entre des abonnements différents](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Transférer la propriété](../../billing/billing-subscription-transfer.md) de votre abonnement 

## <a name="access-for-private-and-public-apps"></a>Accès pour les applications privées et publiques

Pour une application **privée**, l'environnement d'exécution est accessible aux propriétaires et aux contributeurs. Pour une application **publique**, l'environnement d'exécution est accessible à tous les utilisateurs qui disposent de leur propre ressource d'exécution Azure [Cognitive Service](../cognitive-services-apis-create-account.md) ou [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) et de l'ID de l'application publique. 

Aucun catalogue d'applications publiques n'est actuellement disponible.

### <a name="authoring-access"></a>Accès création
L'accès à l'application à partir du portail [LUIS](luis-reference-regions.md#luis-website) ou des [API de création](https://go.microsoft.com/fwlink/?linkid=2092087) est contrôlé par la ressource de création Azure. 

Le propriétaire et tous les contributeurs disposent de l'accès requis pour créer l'application. 

|L’accès à la création inclut|Notes|
|--|--|
|Ajouter ou supprimer des clés de point de terminaison||
|Exporter la version||
|Exporter les journaux d’activité du point de terminaison||
|Importer la version||
|Rendre une application publique|Lorsqu’une application est publique, toute personne disposant d’une clé de création ou de point de terminaison peut interroger l’application.|
|Modifier le modèle|
|Publish|
|Passez en revue les énoncés de point de terminaison pour un [apprentissage actif](luis-how-to-review-endpoint-utterances.md)|
|Former|

### <a name="prediction-endpoint-runtime-access"></a>Accès à l'environnement d'exécution du point de terminaison de prédiction

L'accès permettant d'interroger le point de terminaison de prédiction est contrôlé par un paramètre de la page **Informations sur l'application** dans la section **Gérer**. 

![Définir l’application comme publique](./media/luis-concept-security/set-application-as-public.png)

|[Point de terminaison privé](#runtime-security-for-private-apps)|[Point de terminaison public](#runtime-security-for-public-apps)|
|:--|:--|
|Accessible au propriétaire et aux contributeurs|Accessible au propriétaire, aux contributeurs et à toute personne qui connaît l'ID de l'application|

Vous pouvez contrôler qui voit votre clé d'exécution LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Une fois l'API côté serveur appelée et authentifiée, et l'autorisation vérifiée, passez l'appel à LUIS. Bien que cette stratégie n'empêche pas les attaques de l'intercepteur, elle masque votre clé et l'URL du point de terminaison à vos utilisateurs, vous permet d'effectuer un suivi de l'accès et vous permet d'ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Sécurité d'exécution pour les applications privées

L'environnement d'exécution d'une application privée est uniquement disponible pour :

|Clé et utilisateur|Explication|
|--|--|
|Clé de création du propriétaire| Jusqu’à 1 000 accès au point de terminaison|
|Clés de création collaborateur/contributeur| Jusqu’à 1 000 accès au point de terminaison|
|N'importe quelle clé attribuée à LUIS par un auteur ou un collaborateur/contributeur|Dépend du niveau d’utilisation de la clé|

#### <a name="runtime-security-for-public-apps"></a>Sécurité d'exécution pour les applications publiques

Une fois qu’une application est configurée comme étant publique, _toute_ clé de création LUIS valide ou clé de point de terminaison LUIS peut interroger votre application, tant que la clé n’a pas épuisé le quota de point de terminaison.

Un utilisateur qui n'est pas propriétaire ou contributeur ne peut accéder à l'environnement d'exécution d'une application publique que s'il dispose de l'ID de l'application. LUIS ne dispose pas d’un _marché_ public, ni d’un autre moyen de rechercher une application publique.  

Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à la clé de ressource.

## <a name="transfer-of-ownership"></a>Transfert de propriété

**Pour les applications [migrées avec ressources de création](luis-migration-authoring.md)**  : En tant que propriétaire de la ressource, vous pouvez ajouter un `contributor`.

**Pour les applications qui n'ont pas encore été migrées** : Exportez votre application sous forme de fichier JSON. Un autre utilisateur LUIS peut importer l'application, devenant ainsi le propriétaire de celle-ci. La nouvelle application aura un ID d'application différent.  

## <a name="securing-the-endpoint"></a>Sécurisation du point de terminaison 

Vous pouvez déterminer qui peut voir votre clé d'exécution de point de terminaison de prédiction LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Lorsque l’API côté serveur est appelée et que l’authentification et l’autorisation sont vérifiées, passez l’appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre point de terminaison à vos utilisateurs, vous permet d’effectuer le suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Étapes suivantes

* Comprendre les concepts du [contrôle de version](luis-concept-version.md). 
* Apprenez à [créer des clés](luis-how-to-azure-subscription.md).
