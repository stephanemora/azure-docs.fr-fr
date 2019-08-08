---
title: Aide à la prédiction grâce aux modèles – LUIS
titleSuffix: Azure Cognitive Services
description: Un modèle vous permet d’obtenir plus de précision pour une intention sans fournir de nombreux énoncés en plus.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639201"
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

Si une application présente entre 10 et 20 énoncés de longueurs différentes, avec un ordre de mots, voire même des mots différents (synonymes de « subordonné », « gérer », « rapport »), LUIS peut renvoyer un score de confiance faible. Créez un modèle pour aider LUIS à comprendre l’importance de l’ordre des mots. 

Les modèles peuvent résoudre les situations suivantes : 

* Le score d’intention est faible.
* La bonne intention n’est pas le meilleur score, mais en est trop proche. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Les modèles ne garantissent pas l’intention
Les modèles utilisent un mélange de technologies de prédiction. La définition d’une intention pour un exemple d’énoncé dans un modèle ne garantit pas la prédiction de l’intention, mais constitue un signal fort. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Les modèles n’améliorent pas la détection d’entités apprises automatiquement

Un modèle sert principalement à faciliter la prédiction des intentions et des rôles. L’entité pattern.any permet d’extraire des entités à structure libre. Même s’ils utilisent des entités, les modèles n’aident pas à détecter des entités apprises automatiquement.  

Ne vous attendez pas à constater une amélioration de la prédiction d’entité si vous réduisez plusieurs énoncés dans un modèle unique. Pour que les entités Simple se déclenchent, vous devez ajouter des énoncés ou utiliser des entités de liste, sinon votre modèle ne se déclenchera pas.

## <a name="patterns-use-entity-roles"></a>Les modèles utilisent des rôles d’entités
Si deux ou plusieurs entités dans un modèle sont associées de manière contextuelle, les modèles utilisent des [rôles](luis-concept-roles.md) d’entités pour extraire des informations contextuelles sur les entités.  

## <a name="prediction-scores-with-and-without-patterns"></a>Scores de prédiction avec et sans modèles
Avec suffisamment d’exemples d’énoncés, LUIS doit pouvoir accroître la confiance des prévisions sans modèles. Les modèles augmentent le score de confiance sans avoir à fournir autant énoncés.  

## <a name="pattern-matching"></a>Critères spéciaux
Un modèle est d’abord mis en correspondance selon la détection des entités dans le modèle, puis selon la validation du reste des mots et de l’ordre des mots du modèle. Des entités sont requises dans le modèle pour qu’un modèle corresponde. Le modèle est appliqué au niveau du jeton, pas au niveau du caractère. 

## <a name="pattern-syntax"></a>Syntaxe du modèle
La syntaxe du modèle est un modèle d’énoncé. Le modèle doit contenir les mots et entités que vous souhaitez faire correspondre, ainsi que les mots et la ponctuation que vous souhaitez ignorer. Ce n’est **pas** une expression régulière. 

Les entités des modèles sont placées entre accolades, `{}`. Les modèles peuvent inclure des entités et des entités avec rôles. [Pattern.any](luis-concept-entity-types.md#patternany-entity) est une entité qui n’est utilisée que dans les modèles. 

Les modèles acceptent la syntaxe suivante :

|Fonction|Syntaxe|Niveau d’imbrication|Exemples|
|--|--|--|--|
|entité| {} – accolades|2|Où se trouve le formulaire {nom-entité} ?|
|facultatif|[] – crochets<BR><BR>Les niveaux d’imbrication de la syntaxe facultative et de la syntaxe de regroupement sont limités à trois, pour toutes les combinaisons possibles |2|Le point d’interrogation est facultatif [?]|
|regroupement|() – parenthèses|2|est (a \| b)|
|or| \| – barre verticale (pipe)<br><br>Les barres verticales (ou) sont limitées à deux par groupe |-|Où est le formulaire ({nom-court-formulaire} &#x7c; {nom-long-formulaire} &#x7c; {numéro-formulaire})| 
|Début ou fin de l’énoncé|^ – accent circonflexe (caret)|-|^commence l’énoncé<br>l’énoncé est terminé^<br>^correspondance littérale stricte de tout l’énoncé avec l’entité {numéro}^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxe d’imbrication dans les modèles

La syntaxe **facultatif**, entre crochets, peut être imbriquée sur deux niveaux. Par exemple : `[[this]is] a new form`. Cet exemple accepte les énoncés suivants : 

|Exemple d’énoncé facultatif imbriqué|Explication|
|--|--|
|this is a new form|correspond à tous les mots du modèle|
|is a new form|correspond au mot facultatif externe et aux mots non facultatifs du modèle|
|a new form|correspond uniquement aux mots obligatoires|

La syntaxe **regroupement**, entre parenthèses, peut être imbriquée sur deux niveaux. Par exemple : `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Cette fonctionnalité permet une correspondance avec chacune des trois entités. 

Si Entity1 est un emplacement comportant des rôles comme l’origine (Seattle) et la destination (Cairo) et Entity2 un nom de bâtiment connu issu d’une entité de liste (RedWest-C), les énoncés suivants correspondent à ce modèle :

|Exemple d’énoncé de regroupement imbriqué|Explication|
|--|--|
|RedWest-C|correspond à l’entité de regroupement externe|
|Seattle|correspond à l’une des entités de regroupement internes|
|Cairo|correspond à l’une des entités de regroupement internes|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites d’imbrication des groupes à syntaxe facultative

Les combinaisons entre les syntaxes **regroupement** et **facultatif** sont limitées à trois niveaux d’imbrication.

|Autorisé|Exemples|
|--|--|
|OUI|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Non|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites d’imbrication des groupes à syntaxe de type ou

Les combinaisons entre les syntaxes **regroupement** et **ou** sont limitées à deux barres verticales.

|Autorisé|Exemples|
|--|--|
|OUI|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Non|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

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

Les mots du titre du livre ne gênent pas LUIS, car il sait où le titre du livre se termine grâce à l’entité Pattern.any.

## <a name="explicit-lists"></a>Listes explicites

Créez une [Liste explicite](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via l’API de création pour autoriser l’exception si les deux conditions suivantes sont réunies :

* Votre modèle contient un [Pattern.any](luis-concept-entity-types.md#patternany-entity).
* La syntaxe de ce modèle autorise l’extraction d’une mauvaise entité selon l’énoncé. 

Par exemple, supposons que vous disposez d’un modèle contenant la syntaxe facultative, `[]`, et la syntaxe d’entité, `{}`, combinées de façon à extraire les données de manière incorrecte.

Prenons l’exemple du modèle « [trouver] e-mail sur {objet} [de {personne}] ».

Dans les énoncés suivants, les entités **objet** et **personne** sont extraites correctement et incorrectement :

|Énoncé|Entité|Extraction correcte|
|--|--|:--:|
|e-mail sur les chiens de Chris|objet = les chiens<br>personne = Chris|✔|
|e-mail sur l’homme de La Manche|objet = l’homme<br>personne = La Manche|X|

Dans le tableau précédent, l’objet devrait être `the man from La Mancha` (titre de livre) ; cependant, comme il comporte le mot facultatif `from`, le titre est mal prédit. 

Pour corriger cette exception dans le modèle, ajoutez `the man from la mancha` en tant que correspondance de liste explicite pour l’entité {objet} à l’aide de l’[API de création de liste explicite](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pour marquer le texte facultatif dans un modèle d’énoncé
Marquez un texte facultatif dans l’énoncé à l’aide de la syntaxe en crochet d’expression régulière, `[]`. Dans le texte facultatif, vous pouvez imbriquer jusqu'à deux crochets uniquement.

|Modèle avec un texte facultatif|Signification|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` et `from {person}` sont facultatifs.|
|`Can you help me[?]|Le signe de ponctuation est facultatif|

Les signes de ponctuation (`?`, `!` et `.`) doivent être ignorés, avec la syntaxe entre crochets des modèles. 

## <a name="pattern-only-apps"></a>Applications comportant uniquement des modèles
Il est possible de créer des applications dont les intentions ne comportent aucun exemple d’énoncé, car il existe un modèle pour chaque intention. Le modèle en question ne doit pas contenir d’entités apprises automatiquement, pour lesquelles des exemples d’énoncés sont nécessaires. 

## <a name="best-practices"></a>Bonnes pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprenez à implémenter des modèles dans ce tutoriel](luis-tutorial-pattern.md)
