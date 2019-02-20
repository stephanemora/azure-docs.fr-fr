---
title: limites
titleSuffix: Language Understanding - Azure Cognitive Services
description: Cet article liste les limites connues d’Azure Cognitive Services Language Understanding (LUIS). LUIS comporte plusieurs domaines limites. La limite de modèle contrôle les intentions, les entités et les caractéristiques dans LUIS. La limite de quota dépend du type de clé. La combinaison de touches contrôle le site web de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: df69cb5cc2e369a5e1f372d49a821616bf624db9
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237550"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites pour vos clés et modèle LUIS
LUIS comporte plusieurs domaines limites. Le premier est la [limite de modèle](#model-boundaries), qui contrôle les intentions, les entités et les fonctionnalités dans LUIS. Le deuxième domaine est la [limite de quota](#key-limits), qui est fonction du type de clé. Le troisième domaine de limites est la [combinaison clavier](#keyboard-controls) pour contrôler le site web LUIS. Un quatrième domaine est le [mappage de régions du monde](luis-reference-regions.md) entre le site web de création de LUIS et les API du [point de terminaison](luis-glossary.md#endpoint) de LUIS. 


## <a name="model-boundaries"></a>Limites du modèle

Si votre application dépasse les limites du modèle LUIS, envisagez d’utiliser une application de [distribution LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou un [conteneur LUIS](luis-container-howto.md). 

|Domaine|Limite|
|--|:--|--|
| [Nom de l’application][luis-get-started-create-app] | * Nombre maximum de caractère par défaut |
| [Test par lot][batch-testing]| jeux de 10 données, 1 000 énoncés par jeu de données|
| Liste explicite | 50 par application|
| [Intentions][intents]|500 par application : 499 intentions personnalisées et l’intention _Aucune_ obligatoire.<br>L’application [dispatch](https://aka.ms/dispatch-tool) a 500 sources de dispatch correspondantes.|
| [Répertorier des entités](./luis-concept-entity-types.md) | Parent : 50, Enfant : 20 000 éléments. Le nom canonique est *nombre maximum de caractère par défaut. Les valeurs synonymes n’ont aucune restriction de longueur. |
| [Entités issues de l’apprentissage automatique](./luis-concept-entity-types.md) :<br> Composite<br>  Hiérarchique<br> Simple|Une limite de 100 entités parentes (sans inclure les enfants hiérarchiques) ou de 330 entités (en incluant les enfants hiérarchiques), selon celle que l’utilisateur atteint en premier.<br><br>Un exemple de hiérarchie inclurait 30 hiérarchies avec chacune 10 enfants.  Les enfants consomment un total de 300, tandis que les éléments de la hiérarchie consomment les 30 restants. |
| [Modèles](luis-concept-patterns.md)|500 modèles par application.<br>La longueur maximale du modèle est de 400 caractères.<br>3 entités Pattern.any par modèle<br>2 textes facultatifs maximum imbriqués dans le modèle|
| [Pattern.any](./luis-concept-entity-types.md)|100 par application, 3 entités pattern.any par modèle |
| [Liste d’expressions][phrase-list]|10 listes d’expressions, 5 000 éléments par liste|
| [Entités prédéfinies](./luis-prebuilt-entities.md) | aucune limite|
| [Entité d’expression régulière](./luis-concept-entity-types.md)|20 entités<br>500 caractères maximum par modèle d’entité d’expression régulière|
| [Rôles](luis-concept-roles.md)|300 rôles par application. 10 rôles par entité|
| [Énoncé][utterances] | 500 caractères|
| [Énoncés][utterances] | 15 000 par application|
| [Versions](luis-concept-version.md)| aucune limite |
| [Nom de version][luis-how-to-manage-versions] | 10 caractères restreints à l’alphanumérique et point (.) |

* 50 caractères par défaut maximum. 

## <a name="intent-and-entity-naming"></a>Attribution de noms aux intentions et aux entités
N’utilisez pas les caractères suivants dans les noms des intentions et des entités :

|Caractère|Nom|
|--|--|
|`{`|Accolade gauche|
|`}`|Accolade droite|
|`[`|Crochet gauche|
|`]`|Crochet droit|
|`\`|Barre oblique inverse|

## <a name="key-usage"></a>Utilisation de la clé

Language Understanding possède des clés séparées, un type pour la création et un type pour l'interrogation du point de terminaison de prédiction. Pour en savoir plus sur les différences entre les types de clés, voir [Clés de point de terminaison de création et de prédiction de requête dans LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Limites de clés

La clé de création a différentes limites pour la création et le point de terminaison. La clé de point de terminaison de service LUIS n’est valide que pour les requêtes de point de terminaison.


|Clé|Création|Point de terminaison|Objectif|
|--|--|--|--|
|Création/Starter Language Understanding|1 000 000/mois, 5/seconde|1 000/mois, 5/seconde|Création de votre application LUIS|
|[Abonnement][pricing] Language Understanding - F0 - Niveau gratuit |non valide|10 000 par mois, 5/seconde|Interrogation de votre point de terminaison LUIS|
|[Abonnement][pricing] Language Understanding - S0 - Niveau de base|non valide|50/seconde|Interrogation de votre point de terminaison LUIS|
|[Abonnement][pricing] Cognitive Services - S0 - Niveau standard|non valide|50/seconde|Interrogation de votre point de terminaison LUIS|
|[Intégration de l’analyse des sentiments](luis-how-to-publish-app.md#enable-sentiment-analysis)|non valide|aucun frais|Ajout d’informations sur les sentiments, y compris l’extraction de données de phrases clés |
|Intégration du Speech|non valide|5,50 USD/1 000 requêtes de point de terminaison|Convertir un énoncé vocal en énoncé de texte et retourner des résultats LUIS|

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
