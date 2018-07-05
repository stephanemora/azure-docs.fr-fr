---
title: Découvrir comment les modèles augmentent la précision des prédictions | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment concevoir des modèles pour augmenter les scores de prédiction des intentions et trouver des entités.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr
ms.openlocfilehash: c3c0d12bbbeec85d2cbf0daead49ee16ca7728fb
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046256"
---
# <a name="patterns-improve-prediction-accuracy"></a>Les modèles améliorent la précision de la prédiction
Les modèles sont conçus pour améliorer la précision lorsque plusieurs énoncés sont très similaires. Si vous fournissez un modèle pour l’énoncé, LUIS aura une confiance élevée en la prédiction. 

## <a name="patterns-solve-low-intent-confidence"></a>Les modèles résolvent les problèmes de faible confiance envers les intentions
Imaginez une application Ressources humaines qui génère des rapports sur l’organigramme en lien avec un employé. Selon le nom et les relations d’un employé, LUIS peut retourner les employés impliqués. Imaginez un employé, Tom, avec une manager nommée Alice, et une équipe de subordonnés appelés Michael, Rebecca et Carl.

![Image de l’organigramme](./media/luis-concept-patterns/org-chart.png)

|Énoncés|Intention prédite|Score d’intention|
|--|--|--|
|Qui est subordonné à Tom ?|GetOrgChart|.30|
|Qui est le subordonné de Tom ?|GetOrgChart|.30|

Si une application présente entre 10 et 20 énoncés de longueurs différentes, avec un ordre de mots, voire même des mots différents (synonymes de « subordonné », « gérer », « rapport »), LUIS peut renvoyer un score de confiance faible. Pour aider LUIS à comprendre l’importance de l’ordre des mots, créez un modèle. 

Les modèles peuvent résoudre les situations suivantes : 

* Quand le score d’intention est faible
* Quand la bonne intention n’est pas le meilleur score, mais est très proche du meilleur score. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Les modèles ne garantissent pas l’intention
Les modèles utilisent un mélange de technologies de prédiction. La définition d’une intention pour un exemple d’énoncé dans un modèle ne garantit pas la prédiction de l’intention, mais constitue un signal fort. 

## <a name="patterns-do-not-improve-entity-detection"></a>Les modèles n’améliorent pas la détection d’entités
Même si les modèles nécessitent des entités, un modèle n’aide pas à détecter les entités. Un modèle aide uniquement la prédiction avec les intentions et les rôles.  

## <a name="patterns-use-entity-roles"></a>Les modèles utilisent des rôles d’entités
Si deux ou plusieurs entités dans un modèle sont associées de manière contextuelle, les modèles utilisent des [rôles](luis-concept-roles.md) d’entités pour extraire des informations contextuelles sur les entités. Cela équivaut aux enfants d’entité hiérarchique, mais n’est disponible que dans les modèles **uniquement**. 

## <a name="prediction-scores-with-and-without-patterns"></a>Scores de prédiction avec et sans modèles
Avec suffisamment d’exemples d’énoncés, LUIS doit pouvoir accroître la confiance des prévisions sans modèles. Les modèles augmentent le score de confiance sans avoir à fournir autant énoncés.  

## <a name="pattern-matching"></a>Critères spéciaux
Un modèle est d’abord mis en correspondance selon la détection des entités dans le modèle, puis selon la validation du reste des mots et de l’ordre des mots du modèle. Des entités sont requises dans le modèle pour qu’un modèle corresponde. 

## <a name="pattern-syntax"></a>Syntaxe du modèle
La syntaxe du modèle est un modèle d’énoncé. Le modèle doit contenir les mots et entités que vous souhaitez faire correspondre, ainsi que les mots et la ponctuation que vous souhaitez ignorer. Ce n’est **pas** une expression régulière. 

Les entités des modèles sont placées entre accolades, `{}`. Les modèles peuvent inclure des entités et des entités avec rôles. Pattern.any est une entité seulement utilisée dans les modèles. La syntaxe est expliquée dans les sections suivantes.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pour ajouter une entité à un modèle
Pour ajouter une entité au modèle, placez le nom de l’entité entre accolades, comme `Who does {Employee} manage?`. 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pour ajouter une entité et un rôle à un modèle
Le rôle d’une entité est indiqué par `{entity:role}` avec le nom de l’entité suivi d’un signe deux-points, puis du nom de rôle. Pour ajouter une entité et un rôle au modèle, placez le nom de l’entité et le nom du rôle entre accolades, comme `Book a ticket from {Location:Origin} to {Location:Destination}`. 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pour ajouter un pattern.any à un modèle
L’entité Pattern.any vous permet d’ajouter une entité de longueur variable au modèle. Tant que le modèle est suivi, l’entité pattern.any peut être de n’importe quelle longueur. 

Pour ajouter une entité **Pattern.any** au modèle, placez l’entité Pattern.any entre accolades, comme `How much does {Booktitle} cost and what format is it available in?`.  

```
How much does {Booktitle} cost and what format is it available in?
```

|Titres de livres dans le modèle|
|--|
|Combien **Steal this book** coûte-t-il et dans quel format est-il disponible ?|
|Combien **Soufi, mon amour** coûte-t-il et dans quel format est-il disponible ?|
|Combien **Le Bizarre Incident du chien pendant la nuit** coûte-t-il et dans quel format est-il disponible ?| 

Dans ces exemples de titres de livres, les mots contextuels des titres ne prêtent pas à confusion pour LUIS. LUIS sait où le titre du livre se termine, car il se trouve dans un modèle et est marqué d’une entité Pattern.any.

### <a name="explicit-lists"></a>Listes explicites
Si votre modèle contient une entité Pattern.any, et que la syntaxe du modèle permet d’extraire une entité incorrecte en fonction de l’énoncé, créez une [Liste explicite](https://aka.ms/ExplicitList) via l’API de création pour autoriser l’exception. 

Par exemple, supposons que vous disposez d’un modèle contenant la syntaxe facultative, `[]`, et la syntaxe d’entité, `{}`, combinées de façon à extraire les données de manière incorrecte.

Prenons l’exemple du modèle « [trouver] e-mail sur {objet} [de {personne}] ». Dans les énoncés suivants, les entités **objet** et **personne** sont extraites correctement et incorrectement :

|Énoncé|Entité|Extraction correcte|
|--|--|:--:|
|e-mail sur les chiens de Chris|objet = les chiens<br>personne = Chris|✔|
|e-mail sur l’homme de La Manche|objet = l’homme<br>personne = La Manche|X|

Dans le tableau précédent, dans l’énoncé `email about the man from La Mancha`, l’objet doit être `the man from La Mancha` (titre du livre), mais comme l’objet inclut le mot facultatif `from`, le titre est incorrectement prédit. 

Pour corriger cette exception dans le modèle, ajoutez `the man from la mancha` en tant que correspondance de liste explicite pour l’entité {objet} à l’aide de l’[API de création de liste explicite](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pour marquer le texte facultatif dans un modèle d’énoncé
Marquez un texte facultatif dans l’énoncé à l’aide de la syntaxe en crochet d’expression régulière, `[]`. Dans le texte facultatif, vous pouvez imbriquer jusqu'à deux crochets uniquement.

```
[find] email about {subject} [from {person}]
```

Les signes de ponctuation tels que `.`, `!`, et `?` peuvent être ignorés à l’aide de crochets. Pour pouvoir ignorer ces marques, chaque marque doit se trouver dans un modèle distinct. La syntaxe facultative ne peut actuellement pas ignorer un élément dans une liste de plusieurs éléments.

## <a name="patterns-only"></a>Modèles seulement
LUIS autorise une application sans exemple d’énoncé dans l’intention. Cette utilisation n’est autorisée que si les modèles sont utilisés. Les modèles requièrent au moins une entité dans chaque modèle. Dans les applications de modèle, le modèle ne doit pas contenir d’entités issues de l’apprentissage automatique car elles nécessitent des exemples d’énoncés. 

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprenez à implémenter des modèles dans ce tutoriel](luis-tutorial-pattern.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
