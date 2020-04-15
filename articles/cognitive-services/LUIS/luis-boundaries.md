---
title: Limites - LUIS
description: Cet article liste les limites connues d’Azure Cognitive Services Language Understanding (LUIS). LUIS comporte plusieurs domaines limites. La limite de modèle contrôle les intentions, les entités et les caractéristiques dans LUIS. La limite de quota dépend du type de clé. La combinaison de touches contrôle le site web de LUIS.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618865"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites pour vos clés et modèle LUIS
LUIS comporte plusieurs domaines limites. Le premier est la [limite de modèle](#model-boundaries), qui contrôle les intentions, les entités et les fonctionnalités dans LUIS. Le deuxième domaine est la [limite de quota](#key-limits), qui est fonction du type de clé. Le troisième domaine de limites est la [combinaison clavier](#keyboard-controls) pour contrôler le site web LUIS. Un quatrième domaine est le [mappage de régions du monde](luis-reference-regions.md) entre le site web de création de LUIS et les API du [point de terminaison](luis-glossary.md#endpoint) de LUIS.


## <a name="model-boundaries"></a>Limites du modèle

Si votre application dépasse les limites du modèle LUIS, envisagez d’utiliser une application de [distribution LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou un [conteneur LUIS](luis-container-howto.md).

|Domaine|Limite|
|--|:--|
| [Nom de l’application][luis-get-started-create-app] | \* Nombre maximum de caractère par défaut |
| Applications| 500 applications par ressource de création Azure |
| [Test par lot][batch-testing]| jeux de 10 données, 1 000 énoncés par jeu de données|
| Liste explicite | 50 par application|
| Entités externes | sans limite |
| [Intentions][intents]|500 par application : 499 intentions personnalisées et l’intention _Aucune_ obligatoire.<br>L’application [dispatch](https://aka.ms/dispatch-tool) a 500 sources de dispatch correspondantes.|
| [Répertorier des entités](./luis-concept-entity-types.md) | Parent : 50, Enfant : 20 000 éléments. Le nom canonique est *nombre maximum de caractère par défaut. Les valeurs synonymes n’ont aucune restriction de longueur. |
| [Entités + rôles issus de l’apprentissage automatique](./luis-concept-entity-types.md) :<br> composite,<br>simple,<br>rôles d’entité|Limite de 100 entités parentes ou de 330 entités, selon la limite que l’utilisateur atteint en premier. Un rôle est comptabilisé comme une entité pour les besoins de cette limite. Un exemple est un composite avec une entité simple qui possède 2 rôles : 1 composite + 1 simple + 2 rôles = 4 des 330 entités.<br>Les sous-composants peuvent être imbriqués jusqu’à 5 niveaux.|
|Modèle en tant que fonctionnalité| Nombre maximum de modèles pouvant être utilisés en tant que descripteur (fonctionnalité) sur un modèle spécifique pour avoir 10 modèles. Nombre maximum de listes d’expressions utilisées en tant que descripteur (fonctionnalité) sur un modèle spécifique pour avoir 10 listes d’expressions.|
| [Préversion - Entités de liste dynamique](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listes de 1 000 environ par demande pour interroger le point de terminaison de prédiction|
| [Modèles](luis-concept-patterns.md)|500 modèles par application.<br>La longueur maximale du modèle est de 400 caractères.<br>3 entités Pattern.any par modèle<br>2 textes facultatifs maximum imbriqués dans le modèle|
| [Pattern.any](./luis-concept-entity-types.md)|100 par application, 3 entités pattern.any par modèle |
| [Liste d’expressions][phrase-list]|500 listes d’expressions. 10 listes d’expressions globales liées au modèle en tant que limite de fonctionnalité. La liste d’expressions non interchangeables comprend un maximum de 5 000 expressions. La liste d’expressions interchangeables comprend un maximum de 50 000 expressions. Nombre maximum d’expressions au total par application de 500 000 expressions.|
| [Entités prédéfinies](./luis-prebuilt-entities.md) | aucune limite|
| [Entité d’expression régulière](./luis-concept-entity-types.md)|20 entités<br>500 caractères maximum par modèle d’entité d’expression régulière|
| [Rôles](luis-concept-roles.md)|300 rôles par application. 10 rôles par entité|
| [Énoncé][utterances] | 500 caractères|
| [Énoncés][utterances] | 15 000 par application, il n’existe aucune limite du nombre d’énoncés par intention|
| [Versions](luis-concept-version.md)| 100 versions par application |
| [Nom de version][luis-how-to-manage-versions] | 10 caractères restreints à l’alphanumérique et point (.) |

\* 50 caractères par défaut maximum.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unicité du nom

Utilisez les règles d’unicité d’attribution de noms suivantes.

Les éléments suivants doivent être uniques au sein d’une application LUIS :

* nom de version
* intention
* entité
* roles

Les éléments suivants doivent être uniques au sein de l’étendue appliquée :

* liste d’expressions

## <a name="object-naming"></a>Attribution de noms aux objets

N’utilisez pas les caractères suivants dans les noms suivants.

|Object|Exclure des caractères|
|--|--|
|Noms d’intention, d’entité et de rôle|`:`<br>`$` <br> `&`|
|Nom de version|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Utilisation et limites des ressources

Language Understanding possède des ressources distinctes, un type pour la création et un type pour l'interrogation du point de terminaison de prédiction. Pour en savoir plus sur les différences entre les types de clés, voir [Clés de point de terminaison de création et de prédiction de requête dans LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limites des ressources de création

Utilisez le _genre_, `LUIS.Authoring`, lors du filtrage des ressources dans le portail Azure. LUIS limite à 500 applications par ressource de création Azure.

|Ressource de création|TPS de création|
|--|--|
|Starter|1 000 000/mois, 5/seconde|
|F0 - Niveau gratuit |1 000 000/mois, 5/seconde|

* TPS = Transactions par seconde

[Apprenez-en davantage sur la tarification.][pricing]

### <a name="query-prediction-resource-limits"></a>Interroger les limites des ressources de prédiction

Utilisez le _genre_, `LUIS`, lors du filtrage des ressources dans le portail Azure. La ressource de point de terminaison de prédiction de requête LUIS, utilisée dans le runtime, est uniquement valide pour les requêtes de point de terminaison.

|Interroger la ressource de prédiction|TPS des requêtes|
|--|--|
|F0 - Niveau gratuit |10 000 par mois, 5/seconde|
|S0 - Niveau Standard|50/seconde|

### <a name="sentiment-analysis"></a>analyse de sentiments

L'[intégration de l'analyse des sentiments](luis-how-to-publish-app.md#enable-sentiment-analysis), qui fournit des informations sur les sentiments, est fournie sans nécessiter une autre ressource Azure.

### <a name="speech-integration"></a>Intégration du Speech

L'[intégration du Speech](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fournit 1 000 requêtes de point de terminaison par coût unitaire.

[Apprenez-en davantage sur la tarification.][pricing]

## <a name="keyboard-controls"></a>Commandes du clavier

|Entrée de clavier | Description |
|--|--|
|Contrôle + E|bascule entre les jetons et les entités dans la liste d’énoncés|

## <a name="website-sign-in-time-period"></a>Période de connexion au site web

Votre accès de connexion est valable **60 minutes**. Une fois ce délai expiré, le message d’erreur suivant s’affichera. Vous devez vous reconnecter.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
