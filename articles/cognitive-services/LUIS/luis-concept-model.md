---
title: Concevoir avec des modèles – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding fournit plusieurs types de modèles. Certains modèles peuvent être utilisés de plusieurs manières.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218803"
---
# <a name="design-with-intent-and-entity-models"></a>Conception avec modèles d’intention et d’entité 

Language Understanding fournit plusieurs types de modèles. Certains modèles peuvent être utilisés de plusieurs manières. 

## <a name="v3-authoring-uses-machine-teaching"></a>Création V3 utilise le Machine Teaching

LUIS permet aux utilisateurs d’enseigner facilement des concepts à un ordinateur. Celui-ci peut ensuite créer des modèles (approximations fonctionnelles de concepts tels que des classificateurs et des extracteurs) utilisable pour alimenter des applications intelligentes. Bien que LUIS soit optimisé par le Machine Learning, aucune compréhension préalable de cette technologie n’est nécessaire pour l’utiliser. Au lieu de cela, les intervenants chargés d’effectuer l’apprentissage communiquent des concepts à LUIS en montrant des exemples positifs et négatifs de ceux-ci et en expliquant comment un concept doit être modélisé à l’aide d’autres concepts connexes. Les intervenants peuvent également améliorer le modèle de LUIS de manière interactive en identifiant et en corrigeant les erreurs de prédiction. 

## <a name="v3-authoring-model-decomposition"></a>Décomposition de modèle de Création V3

LUIS prend en charge la _décomposition de modèle_ avec des API de création V3, décomposant le modèle en éléments plus petits. Cela vous permet de construire vos modèles en toute confiance dans la manière dont les différents éléments sont construits et prédits.

La décomposition du modèle comprend les éléments suivants :

* [intentions](#intents-classify-utterances)
    * [descripteurs](#descriptors-are-features) fournis par des caractéristiques
* [entités issues du Machine Learning](#machine-learned-entities)
    * [sous-components](#entity-subcomponents-help-extract-data) (également des entités issues du Machine Learning)
        * [descripteurs](#descriptors-are-features) fournis par des caractéristiques 
        * [contraintes](#constraints-are-text-rules) fournies par des entités non issues de machine learning, telles que des expressions régulières et des listes

## <a name="v2-authoring-models"></a>Modèles de Création v2

LUIS prend en charge des entités composites avec les API de Création V2. Ceci fournit une décomposition de modèle similaire mais pas identique à la décomposition effectuée par le modèle V3. L’architecture de modèle recommandée consiste à déplacer la décomposition de modèle vers les API de Création V3. 

## <a name="intents-classify-utterances"></a>Les intentions classifient les énoncés

Une intention classifie des exemples d’énoncés à enseigner à LUIS à propos de l’intention. Les exemples d’énoncés dans une intention sont utilisés comme exemples positifs d’énoncé. Ces mêmes énoncés sont utilisés comme exemples négatifs dans toutes les autres intentions.

Considérons une application qui doit déterminer l’intention d’un utilisateur de commander un livre et une application qui a besoin de l’adresse de livraison du client. Cette application a deux intentions : `OrderBook` et `ShippingLocation`.

L’énoncé suivant est un **exemple positif** pour l’intention `OrderBook` et un **exemple négatif** pour les intentions `ShippingLocation` et `None` : 

`Buy the top-rated book on bot architecture.`

Le résultat d’intentions bien conçues, avec leurs exemples d’énoncés, est une prédiction d’intention élevée. 

## <a name="entities-extract-data"></a>Les entités extraient les données

Une entité représente une unité de données que vous souhaitez extraire de l’énoncé. 

### <a name="machine-learned-entities"></a>Entités issues du Machine Learning

Une entité issue du Machine Learning est une entité de niveau supérieur contenant des sous-composants qui sont également des entités issues du Machine Learning. 

**Utilisez une entité issue du Machine Learning** :

* quand l’application cliente a besoin des sous-composants
* pour aider l’algorithme de Machine Learning à décomposer les entités

Chaque sous-composant peut comprendre les éléments suivants :

* sous-composants
* contraintes (entité d’expression régulière ou entité de liste)
* descripteurs (caractéristiques telles qu’une liste de phrases) 

Un exemple d’entité issue du Machine Learning est une commande de billet d’avion. Conceptuellement, il s’agit d’une transaction unique comportant de nombreuses unités de données plus petites, telles que la date, l’heure, le nombre de places, le type de siège, le lieu d’origine, le lieu de destination et le choix du repas.


### <a name="entity-subcomponents-help-extract-data"></a>Les sous-composants de l’entité aident à extraire des données

Un sous-composant est une entité enfant issue du Machine Learning au sein d’une entité parente issue du Machine Learning. 

**Utilisez le sous-composant pour** :

* décomposer les éléments de l’entité issue du Machine Learning (entité parente).

Ce qui suit représente une entité issue du Machine Learning avec toutes ces éléments de données distincts :

* TravelOrder (entité issue du Machine Learning)
    * DateTime (valeur datetimeV2 prédéfinie)
    * Location (entité issue du Machine Learning)
        * Origin (rôle trouvé via le contexte, par exemple `from`)
        * Destination (rôle trouvé via le contexte, par exemple `to`)
    * Seating (entité issue du Machine Learning)
        * Quantity (nombre prédéfini)
        * Quality (entité issue du Machine Learning avec descripteur de liste de phrases)
    * Meals (entité issue du Machine Learning avec contrainte d’entité de liste telle que des choix alimentaires)

Certaines de ces données, telles que le lieu d’origine et le lieu de destination, doivent être apprises à partir du contexte de l’énoncé, avec éventuellement des formulations telles que `from` et `to`. D’autres éléments de données peuvent être extraits avec des correspondances de chaîne exactes (`Vegan`) ou des entités prédéfinies (geographyV2 de `Seattle` et `Cairo`). 

Vous concevez la manière dont les données sont mises en correspondance et extraites, par quels modèles et comment vous les configurez.

### <a name="constraints-are-text-rules"></a>Les contraintes sont des règles de texte

Une contrainte est une règle de correspondance de texte fournie par une entité non issue du Machine Learning, telle qu’une entité d’expression régulière ou une entité de liste. La contrainte est appliquée au moment de la prédiction pour limiter celle-ci et fournir la résolution d’entité requise par l’application cliente. Vous définissez ces règles lors de la création du sous-composant. 

**Utilisez une contrainte** :
* lorsque vous connaissez le texte exact à extraire.

Les contraintes sont les suivantes :

* entités d’[expression régulière](reference-entity-regular-expression.md)
* entités de [liste](reference-entity-list.md) 
* entités [prédéfinies](luis-reference-prebuilt-entities.md)

En gardant l’exemple du billet d’avion, les codes d’aéroport peuvent figurer dans une entité de liste pour des correspondances de texte exactes. 

Pour une liste d’aéroports, l’entrée de liste pour Seattle est le nom de la ville, `Seattle`. Les synonymes de Seattle incluent le code de l’aéroport de Seattle ainsi des villes et localités environnantes :

|Synonymes de l’entité de liste `Seattle`|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Si vous souhaitez reconnaître uniquement les codes de 3 lettres pour les codes d’aéroport, utilisez une expression régulière comme contrainte. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intentions et entités

Une intention est le résultat souhaité de l’énoncé _entier_, tandis que des entités sont des éléments de données extraits de l’énoncé. Habituellement, les intentions sont liées à des actions que l’application cliente doit effectuer, et les entités sont des informations nécessaires pour effectuer ces actions. Du point de vue de la programmation, une intention déclenche un appel de méthode et les entités sont utilisées comme paramètres de cet appel de méthode.

Cet énoncé _doit_ avoir une intention et _peut_ avoir des entités :

`Buy an airline ticket from Seattle to Cairo`

Cet énoncé n’a qu’une seule intention :

* Achat d’un billet d’avion

Cet énoncé _peut_ avoir plusieurs entités :

* Emplacements de Seattle (origine) et Caire (destination)
* Quantité d’un billet nique

## <a name="descriptors-are-features"></a>Les descripteurs sont des caractéristiques

Un descripteur est une caractéristique appliquée à un modèle au moment de l’apprentissage. Il peut s’agir de listes et d’entités d’expressions. 

**Utilisez un descripteur lorsque vous souhaitez** :

* augmenter l’importance de mots et expressions identifiés par le descripteur
* demander à LUIS de recommander un nouveau texte ou de nouvelles expressions à suggérer pour le descripteur
* corriger une erreur sur les données d’apprentissage

## <a name="next-steps"></a>Étapes suivantes

* Comprendre les [intentions](luis-concept-intent.md) et les [entités](luis-concept-entity-types.md). 