---
title: Limites de Language Understanding (LUIS) | Microsoft Docs
titleSuffix: Azure
description: Cet article traite des limites connues de LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 7f46e55e11c4eb68b515a743b0f51392ffc1269e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266802"
---
# <a name="luis-boundaries"></a>Limites de LUIS
LUIS comporte plusieurs domaines limites. Le premier est la [limite de modèle](#model-boundaries), qui contrôle les intentions, les entités et les fonctionnalités dans LUIS. Le deuxième domaine est la [limite de quota](#key-limits), qui est fonction du type de clé. Le troisième domaine de limites est la [combinaison clavier](#keyboard-controls) pour contrôler le site web LUIS. Un quatrième domaine est le [mappage de régions du monde](luis-reference-regions.md) entre le site web de création de LUIS et les API du [point de terminaison](luis-glossary.md#endpoint) de LUIS. 


## <a name="model-boundaries"></a>Limites du modèle

|Domaine|Limite|
|--|:--|--|
| [Nom de l’application][luis-get-started-create-app] | * Nombre maximum de caractère par défaut |
| [Test par lot][batch-testing]| jeux de 10 données, 1 000 énoncés par jeu de données|
| **[Composite](./luis-concept-entity-types.md)|100 avec jusqu'à 10 enfants |
| Liste explicite | 50 par application|
| **[Hiérarchique](./luis-concept-entity-types.md) |100 avec jusqu'à 10 enfants |
| [Intentions][intents]|500 par application<br>L’application [basée sur la répartition](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) a 500 sources de répartition correspondantes|
| [Répertorier des entités](./luis-concept-entity-types.md) | Parent : 50, enfant : 20 000 éléments. Le nom canonique est ** nombre maximum de caractère par défaut. Les synonymes n’ont aucune restriction de longueur. |
| [Modèles](luis-concept-patterns.md)|500 modèles par application.<br>La longueur maximale du modèle est de 400 caractères.<br>3 entités Pattern.any par modèle<br>2 textes facultatifs maximum imbriqués dans le modèle|
| [Pattern.any](./luis-concept-entity-types.md)|100 par application, 3 entités pattern.any par modèle |
| [Liste d’expressions][phrase-list]|10 listes d’expressions, 5 000 éléments par liste|
| [Entités prédéfinies](./Pre-builtEntities.md) | aucune limite|
| [Entité d’expression régulière](./luis-concept-entity-types.md)|20 entités<br>500 caractères maximum par modèle d’entité d’expression régulière|
| [Rôles](luis-concept-roles.md)|300 rôles par application. 10 rôles par entité|
| **[Simple](./luis-concept-entity-types.md)| 100 entités|
| [Énoncé][utterances] | 500 caractères|
| [Énoncés][utterances] | 15 000 par application|
| [Nom de version][luis-how-to-manage-versions] | 10 caractères restreints à l’alphanumérique et point (.) |

* 50 caractères par défaut maximum. 

** Le nombre total d’entités simples, hiérarchiques et composites ne peut pas dépasser 100. Le nombre total d’entités hiérarchiques, d’entités composites, d’entités simples et d’entités enfants hiérarchiques ne peut pas dépasser 330. 

## <a name="intent-and-entity-naming"></a>Attribution de noms aux intentions et aux entités
N’utilisez pas les caractères suivants dans les noms des intentions et des entités :

|Caractère|NOM|
|--|--|
|`{`|Accolade gauche|
|`}`|Accolade droite|
|`[`|Crochet gauche|
|`]`|Crochet droit|
|`\`|Barre oblique inverse|

## <a name="key-limits"></a>Limites de clés
La clé de création a différentes limites pour la création et le point de terminaison. La clé d’abonnement au service LUIS est uniquement valide pour les requêtes de point de terminaison.

|Clé|Création|Point de terminaison|Objectif|
|--|--|--|--|
|Création/Starter|1 000 000/mois, 5/seconde|1 000/mois, 5/seconde|Création de votre application LUIS|
|[Abonnement][pricing] - F0 - Niveau gratuit |non valide|10 000 par mois, 5/seconde|Interrogation de votre point de terminaison LUIS|
|[Abonnement][pricing] - S0 - niveau de base|non valide|50/seconde|Interrogation de votre point de terminaison LUIS|
|[Intégration de l’analyse des sentiments](publishapp.md#enable-sentiment-analysis)|non valide|aucun frais|Ajout d’informations sur les sentiments, y compris l’extraction de données de phrases clés |
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
