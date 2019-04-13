---
title: Aide à la prédiction grâce aux modèles
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un modèle vous permet d’obtenir plus de précision pour une intention sans fournir de nombreux énoncés en plus.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 2a160ab7447304dc6eb14f76a723df4e8a4d9f46
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523101"
---
# <a name="patterns-improve-prediction-accuracy"></a>Les modèles améliorent la précision de la prédiction
Les modèles sont conçus pour améliorer la précision lorsque plusieurs énoncés sont très similaires.  Un modèle vous permet d’obtenir plus de précision pour une intention sans fournir de nombreux énoncés en plus. 

## <a name="patterns-solve-low-intent-confidence"></a>Les modèles résolvent les problèmes de faible confiance envers les intentions
Imaginez une application Ressources humaines qui génère des rapports sur l’organigramme en lien avec un employé. Selon le nom et les relations d’un employé, LUIS peut retourner les employés impliqués. Imaginez un employé, Tom, avec une manager nommée Alice et une équipe de subordonnés qui s’appellent Michael, Rebecca et Carl.

![Image de l’organigramme](./media/luis-concept-patterns/org-chart.png)

|Énoncés|Intention prédite|Score d’intention|
|--|--|--|
|Qui est subordonné à Tom ?|GetOrgChart|.30|
|Qui est le subordonné de Tom ?|GetOrgChart|.30|

Si une application présente entre 10 et 20 énoncés de longueurs différentes, avec un ordre de mots, voire même des mots différents (synonymes de « subordonné », « gérer », « rapport »), LUIS peut renvoyer un score de confiance faible. Créer un modèle pour aider les LUIS à comprendre l’importance de l’ordre des mots. 

Les modèles peuvent résoudre les situations suivantes : 

* Le score intention est faible
* L’objectif correct n’est pas le score supérieur, mais trop proches au score supérieur. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Les modèles ne garantissent pas l’intention
Les modèles utilisent un mélange de technologies de prédiction. La définition d’une intention pour un exemple d’énoncé dans un modèle ne garantit pas la prédiction de l’intention, mais constitue un signal fort. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Modèles n’améliorent pas la détection de la machine a appris une entité

Un modèle est principalement destiné à la prédiction des intentions et des rôles. L’entité pattern.any est utilisée pour extraire les entités de forme libre. Tandis que les modèles utilisent des entités, un modèle ne permet pas de détecter une entité de la machine a appris.  

Ne vous attendez pas à constater une amélioration de la prédiction d’entité si vous réduisez plusieurs énoncés dans un modèle unique. Pour que les entités Simple se déclenchent, vous devez ajouter des énoncés ou utiliser des entités de liste, sinon votre modèle ne se déclenchera pas.

## <a name="patterns-use-entity-roles"></a>Les modèles utilisent des rôles d’entités
Si deux ou plusieurs entités dans un modèle sont associées de manière contextuelle, les modèles utilisent des [rôles](luis-concept-roles.md) d’entités pour extraire des informations contextuelles sur les entités.  

## <a name="prediction-scores-with-and-without-patterns"></a>Scores de prédiction avec et sans modèles
Avec suffisamment d’exemples d’énoncés, LUIS doit pouvoir accroître la confiance des prévisions sans modèles. Les modèles augmentent le score de confiance sans avoir à fournir autant énoncés.  

## <a name="pattern-matching"></a>Critères spéciaux
Un modèle est d’abord mis en correspondance selon la détection des entités dans le modèle, puis selon la validation du reste des mots et de l’ordre des mots du modèle. Des entités sont requises dans le modèle pour qu’un modèle corresponde. Le modèle est appliqué au niveau du jeton, pas au niveau du caractère. 

## <a name="pattern-syntax"></a>Syntaxe du modèle
La syntaxe du modèle est un modèle d’énoncé. Le modèle doit contenir les mots et entités que vous souhaitez faire correspondre, ainsi que les mots et la ponctuation que vous souhaitez ignorer. Ce n’est **pas** une expression régulière. 

Les entités des modèles sont placées entre accolades, `{}`. Les modèles peuvent inclure des entités et des entités avec rôles. [Pattern.Any](luis-concept-entity-types.md#patternany-entity) est une entité uniquement utilisée dans les modèles. 

Syntaxe de modèle prend en charge la syntaxe suivante :

|Fonction|Syntaxe|Niveau d’imbrication|Exemples|
|--|--|--|--|
|entité| {} -accolades|2|Où se trouve le formulaire {entité-name} ?|
|facultatif|[] - des crochets<BR><BR>Il existe une limite de 3 sur les niveaux d’imbrication de n’importe quelle combinaison de facultatif et de regroupement |2|Le point d’interrogation est facultatif [ ?]|
|regroupement|() - parenthèses|2|est (un \| b).|
|or| \| -vertical de la barre (barre verticale)<br><br>Il existe une limite de 2 sur les barres verticales (ou) dans un groupe |-|Où est le formulaire ({formulaire-nom-court} &#x7c; {formulaire-nom-long} &#x7c; {format-number})| 
|début ou fin de l’énoncé|^-signe insertion|-|^ commencer l’énoncé<br>l’énoncé est effectuée ^<br>^ strict correspondance littéral d’entier énoncé avec l’entité de {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxe d’imbrication dans les modèles

Le **facultatif** syntaxe, avec des crochets, peut être imbriquées deux niveaux. Par exemple : `[[this]is] a new form`. Cet exemple autorise les énoncés suivants : 

|Exemple d’énoncé facultatifs imbriqués|Explication|
|--|--|
|Il s’agit d’un nouveau formulaire|correspond à tous les mots dans le modèle|
|est une nouvelle forme|correspond au mot facultatif externe et les mots non facultatifs dans le modèle|
|un nouveau formulaire|mots de correspondances requis uniquement|

Le **regroupement** syntaxe, avec des parenthèses, peut être imbriquées deux niveaux. Par exemple : `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Cette fonctionnalité permet une des trois entités à mettre en correspondance. 

Si Entity1 est un emplacement avec les rôles, tels que l’origine (Seattle) et de destination (Le Caire) et 2 de l’entité est un nom connu de construction d’une entité de liste (RedWest-C), les énoncés suivants seraient mappée à ce modèle :

|Exemple d’énoncé de regroupement imbriquées|Explication|
|--|--|
|RedWest-C|correspond à des entités de groupement externe|
|Seattle|correspond à l’une des entités de regroupement interne|
|Le Caire|correspond à l’une des entités de regroupement interne|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Imbrication des limites pour les groupes de syntaxe facultatifs

Une combinaison de **regroupement** avec **facultatif** syntaxe a une limite de 3 niveaux d’imbrication.

|Autorisé|Exemples|
|--|--|
|Oui|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Non |( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Imbrication des limites pour les groupes avec la syntaxe d’ou en intégrant

Une combinaison de **regroupement** avec **ou en intégrant** syntaxe a une limite de 2 barres verticales.

|Autorisé|Exemples|
|--|--|
|Oui|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Non |( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pour ajouter une entité à un modèle
Pour ajouter une entité au modèle, placez le nom de l’entité entre accolades, comme `Who does {Employee} manage?`. 

|Modèle avec une entité|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pour ajouter une entité et un rôle à un modèle
Le rôle d’une entité est indiqué par `{entity:role}` avec le nom de l’entité suivi d’un signe deux-points, puis du nom de rôle. Pour ajouter une entité et un rôle au modèle, placez le nom de l’entité et le nom du rôle entre accolades, comme `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Modèle avec des rôles d’entités|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pour ajouter un pattern.any à un modèle
L’entité Pattern.any vous permet d’ajouter une entité de longueur variable au modèle. Tant que le modèle est suivi, l’entité pattern.any peut être de n’importe quelle longueur. 

Pour ajouter une entité **Pattern.any** au modèle, placez l’entité Pattern.any entre accolades, comme `How much does {Booktitle} cost and what format is it available in?`.  

|Modèle avec une entité Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Titres de livres dans le modèle|
|--|
|Combien **Steal this book** coûte-t-il et dans quel format est-il disponible ?|
|Combien **Soufi, mon amour** coûte-t-il et dans quel format est-il disponible ?|
|Combien **Le Bizarre Incident du chien pendant la nuit** coûte-t-il et dans quel format est-il disponible ?| 

Les mots du titre du livre ne sont pas LUIS déroutants car LUIS sait où le titre du livre se termine, basé sur l’entité Pattern.any.

## <a name="explicit-lists"></a>Listes explicites

créer un [liste explicite](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via l’API de création pour autoriser l’exception lorsque :

* Votre modèle contient un [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* Et que la syntaxe de modèle permet à l’éventualité d’une extraction d’entité incorrecte selon l’énoncé. 

Par exemple, supposons que vous disposez d’un modèle contenant la syntaxe facultative, `[]`, et la syntaxe d’entité, `{}`, combinées de façon à extraire les données de manière incorrecte.

Prenons l’exemple du modèle « [trouver] e-mail sur {objet} [de {personne}] ».

Dans les énoncés suivants, les entités **objet** et **personne** sont extraites correctement et incorrectement :

|Énoncé|Entité|Extraction correcte|
|--|--|:--:|
|e-mail sur les chiens de Chris|objet = les chiens<br>personne = Chris|✔|
|e-mail sur l’homme de La Manche|objet = l’homme<br>personne = La Manche|X|

Dans le tableau précédent, le sujet doit être `the man from La Mancha` (titre de livre), mais étant donné que l’objet inclut le mot facultatif `from`, le titre est prédit de façon incorrecte. 

Pour corriger cette exception dans le modèle, ajoutez `the man from la mancha` en tant que correspondance de liste explicite pour l’entité {objet} à l’aide de l’[API de création de liste explicite](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pour marquer le texte facultatif dans un modèle d’énoncé
Marquez un texte facultatif dans l’énoncé à l’aide de la syntaxe en crochet d’expression régulière, `[]`. Dans le texte facultatif, vous pouvez imbriquer jusqu'à deux crochets uniquement.

|Modèle avec un texte facultatif|Signification|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` et `from {person}` sont facultatives|
|« Pouvez-vous m’aider [ ?] | Le signe de ponctuation est facultatif|

Signes de ponctuation (`?`, `!`, `.`) doivent être ignorées et vous devez les ignorer à l’aide de la syntaxe d’un crochet dans les modèles. 

## <a name="pattern-only-apps"></a>Applications de modèle uniquement
Vous pouvez créer une application avec des intentions ayant aucune énoncés exemple, tant qu’il existe un modèle pour chaque objectif. Pour une application de modèle uniquement, le modèle ne doit pas contenir des entités machine a appris, car ils nécessitent des énoncés d’exemple. 

## <a name="best-practices"></a>Bonnes pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprenez à implémenter des modèles dans ce tutoriel](luis-tutorial-pattern.md)
