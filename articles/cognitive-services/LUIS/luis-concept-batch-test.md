---
title: Test par lot - LUIS
titleSuffix: Azure Cognitive Services
description: Utilisez des tests par lot pour travailler en continu sur votre application afin d’affiner et d’améliorer sa compréhension de la langue.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218823"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Tests par lot avec 1000 énoncés dans le portail de LUIS

Les tests par lot valident votre version entraînée active afin de mesurer la précision des prédictions. Un test par lot vous permet de visualiser la précision de chaque intention et entité dans votre version active, affichant ainsi les résultats avec un graphique. Passez en revue les résultats des tests par lot pour prendre les mesures appropriées afin d’améliorer la précision, par exemple ajouter d’autres exemples d’énoncés à une intention si votre application échoue fréquemment dans sa tentative d’identification de l’intention ou étiqueter des entités dans l’énoncé.

## <a name="group-data-for-batch-test"></a>Regrouper les données pour le test par lot

Il est important que les énoncés utilisés pour les tests par lot soient nouveaux pour LUIS. Si vous avez un jeu de données d’énoncés, divisez les énoncés en trois jeux : exemples d’énoncés ajoutés à une intention, énoncés reçus du point de terminaison publié et énoncés utilisés pour exécuter des tests par lot sur LUIS une fois qu’il a été entraîné. 

## <a name="a-data-set-of-utterances"></a>Un jeu de données d’énoncés

Envoyez un fichier d’énoncés, appelé *jeu de données*, pour les tests par lot. Le jeu de données est un fichier au format JSON contenant un maximum de 1 000 énoncés étiquetés et **non dupliqués**. Vous pouvez tester jusqu’à 10 jeux de données dans une application. Si vous avez besoin d’en tester davantage, supprimez un jeu de données, puis ajoutez-en un nouveau.

|**Règles**|
|--|
|*Aucun énoncé en double|
|1000 énoncés ou moins|

*Les doublons sont considérés comme des correspondances de chaînes exactes, et non comme des correspondances qui sont tokenizées en premier. 

## <a name="entities-allowed-in-batch-tests"></a>Entités autorisées dans les tests de lots

Toutes les entités personnalisées de ce modèle s’affichent dans le filtre d’entités de test de lot, même s’il n’y a aucune entité correspondante dans les données du fichier de commandes.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Format de fichier de traitement par lot

Le fichier de traitement par lot est constitué d’énoncés. Chaque énoncé doit avoir une prédiction d’intention attendue ainsi que toute [entité d’apprentissage automatique](luis-concept-entity-types.md#types-of-entities) censée être détectée. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modèle de syntaxe de lot pour les intentions avec des entités

Utilisez le modèle suivant pour démarrer votre fichier de commandes :

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Le fichier de commandes utilise les propriétés **startPos** et **endPos** pour noter le début et la fin d’une entité. Les valeurs commencent à zéro ; elles ne doivent ni commencer ni se terminer par un espace. Les journaux d’activité de requêtes, eux, utilisent les propriétés startIndex et endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Modèle de syntaxe de lot pour les intentions sans entité

Utilisez le modèle suivant pour démarrer votre fichier de commandes sans entité :

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Si vous ne souhaitez pas tester d’entités, incluez la propriété `entities` et définissez la valeur en tant que tableau vide, `[]`.


## <a name="common-errors-importing-a-batch"></a>Erreurs courantes d’importation de lot

Les erreurs courantes sont les suivantes : 

> * Plus de 1000 énoncés
> * Objet JSON d’énoncé n’ayant pas de propriété d’entités. La propriété peut être un tableau vide.
> * Mot(s) étiqueté(s) dans plusieurs entités
> * Étiquette d’entité commençant ou se terminant par un espace.

## <a name="batch-test-state"></a>État du test par lot

LUIS effectue le suivi de l’état du dernier test de chaque jeu de données. Cela inclut la taille (nombre d’énoncés du lot), la date de la dernière exécution et le dernier résultat (nombre d’énoncés prédits correctement).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Résultats du test par lot

Le résultat du test par lot est un graphe à nuages de points, appelé matrice d’erreur. Ce graphe est une quadruple comparaison des énoncés dans le fichier de commandes et de l’intention et des entités prédits du modèle actuel. 

Les points de données des sections **Faux positif** et **Faux négatif** indiquent des erreurs qui doivent être examinées. Si tous les points de données figurent dans les sections **Vrai positif** et **Vrai négatif**, la précision de votre application est parfaite sur ce jeu de données.

![Quatre sections du graphique](./media/luis-concept-batch-test/chart-sections.png)

Ce graphique vous permet d’identifier les énoncés que LUIS prédit correctement d’après son entraînement actuel. Les résultats sont affichés par région du graphique. Sélectionnez des points spécifiques sur le graphe pour consulter les informations d’énoncés ou sélectionnez un nom de région pour consulter les résultats d’énoncés dans cette région.

![Test par lot](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erreurs dans les résultats

Les erreurs dans le test par lot indiquent des intentions qui ne sont pas prédites comme indiqué dans le fichier de traitement par lot. Les erreurs sont indiquées dans les deux sections en rouge du graphique. 

La section Faux positif indique qu’un énoncé a été mis en correspondance avec une intention ou une entité alors qu’il n’aurait pas dû l’être. La section Faux négatif indique qu’un énoncé n’a pas été mis en correspondance avec une intention ou une entité alors qu’il aurait dû l’être. 

## <a name="fixing-batch-errors"></a>Correction des erreurs de lot

S’il existe des erreurs dans le test par lot, vous pouvez ajouter des énoncés à une intention et/ou étiqueter davantage d’énoncés avec l’entité afin d’aider LUIS à effectuer la distinction entre les intentions. Si vous avez ajouté des énoncés et que vous les avez étiquetés, mais que des erreurs se produisent encore dans le test par lot, essayez d’ajouter une fonctionnalité de [liste d’expressions](luis-concept-feature.md) avec un vocabulaire propre au domaine pour aider LUIS à apprendre plus rapidement. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [tester un lot](luis-how-to-batch-test.md)
