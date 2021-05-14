---
title: Types d’entités - LUIS
description: Une entité extrait des données d’un énoncé de l’utilisateur au moment de l’exécution de la prédiction. Un objectif secondaire _facultatif_ est d’améliorer la prédiction de l’intention ou d’autres entités à l’aide de l’entité en tant que caractéristique.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/26/2021
ms.openlocfilehash: a075a84322dc11be352470d50478979b975f0292
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108140890"
---
# <a name="entities-in-luis"></a>Entités dans LUIS

Une entité est un élément relatif à l’intention de l’utilisateur. Les entités définissent des données qui peuvent être extraites de l’énoncé et qui sont essentielles pour effectuer l’action demandée par l’utilisateur. Par exemple :

|Énoncé|Intention prédite|Entités extraites|Explication|
|--|--|--|--|
|Bonjour, comment ça va ?|Greeting|-|Rien à extraire.|
|Je veux commander une petite pizza|CommanderPizza| « petite » | L’entité « taille » est extraite avec la valeur « petite ».|
|Éteindre la lumière de la chambre|Éteindre| « chambre » | L’entité « pièce » est extraite avec la valeur « chambre ».|
|Consulter le solde dans mon compte d’épargne se terminant par 4406|ConsulterSolde| « épargne », « 4406 » | L’entité « TypeCompte » est extraite avec la valeur « épargne » et l’entité « NuméroCompte » est extraite avec la valeur « 4406 ».|
|Acheter 3 tickets pour New York|AcheterBillets| « 3 », « New-York » | L’entité « NombreBillets » est extraite avec la valeur « 3 » et l’entité « Destination » est extraite avec la valeur « New-York ».|

Les entités sont facultatives mais recommandées. Vous n’avez pas besoin de créer des entités pour chaque concept de votre application, mais seulement pour les types de concepts suivants :

* Ceux où l’application cliente a besoin de données, ou 
* Ceux où l’entité sert d’indication ou de signal à une autre entité ou intention. Pour plus d’informations sur les entités en tant que fonctionnalités, consultez [Entités en tant que fonctionnalités](#entities-as-features).

## <a name="entity-types"></a>Types d’entités

Pour créer une entité, vous devez lui attribuer un nom et un type. Il existe plusieurs types d’entités dans LUIS. 

## <a name="list-entity"></a>Entité de liste

Une entité de liste représente un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes. Vous pouvez utiliser des entités de liste afin de reconnaître plusieurs synonymes ou variations, et extraire une sortie normalisée pour ceux-ci. Utilisez la fonctionnalité *recommend* pour voir des suggestions de nouveaux mots en fonction de la liste actuelle. 

Une entité de liste n’est pas issue du machine learning, ce qui signifie que LUIS ne découvre pas de valeurs supplémentaires pour les entités de liste. LUIS marque comme entité chaque correspondance avec un élément d’une liste dans la réponse.

Dans les entités de liste, les correspondances sont sensibles à la casse et doivent donc être exactes pour être extraites. Les valeurs normalisées sont également utilisées lorsque vous établissez une correspondance avec une entité de liste. Par exemple :

|Valeur normalisée|Synonymes|
|--|--|
|Petite|sm, sml, tiny, smallest|
|Moyenne|md, mdm, regular, average, middle|
|grand|lg, lrg, big|

Pour plus d’informations, consultez l’[article de référence sur les entités de liste](reference-entity-list.md).

## <a name="regex-entity"></a>Entité regex

Une entité d’expression régulière extrait une entité en fonction du modèle d’expression régulière que vous fournissez. Elle ignore la casse et la variante culturelle. Une expression régulière est idéale pour du texte structuré ou pour une séquence prédéfinie de valeurs alphanumériques qui sont attendues dans un certain format. Par exemple :

|Entité|Expression régulière|Exemple|
|--|--|--|
|Numéro de vol|vol [A-Z]{2} [0-9]{4}| vol AS 1234|
|Numéro de carte de crédit|[0-9]{16}|5478789865437632|

Pour plus d’informations, consultez l’[article de référence sur les entités regex](reference-entity-regular-expression.md).

## <a name="prebuilt-entity"></a>Entité prédéfinie

LUIS offre un ensemble d’entités prédéfinies qui permet de reconnaître les types de données courants tels que les noms, les dates, les nombres et les devises.  Le comportement des entités prédéfinies est fixe. La prise en charge des entités prédéfinies varie en fonction de la culture de l’application LUIS. Par exemple :

|Entité prédéfinie|Valeur d'exemple|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2 mai, 8 h 00 le 2 mai 2019|

Pour plus d’informations, consultez l’[article de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md).

## <a name="patternany-entity"></a>Entité Pattern.Any

Une entité pattern.Any est un espace réservé de longueur variable qui est utilisé seulement dans l’énoncé d’un modèle pour marquer où l’entité commence et où elle se termine. Elle suit une règle ou modèle spécifique, et convient davantage aux phrases qui ont une structure lexicale fixe. Par exemple :

|Exemple d’énoncé|Modèle|Entité|
|--|--|--|
|Puis-je avoir un hamburger ?|Puis-je avoir un {plat} [s’il vous plaît] [?]| hamburger
|Puis-je avoir une pizza ?|Puis-je avoir un {plat} [s’il vous plaît] [?]| pizza
|Où puis-je trouver Gatsby le Magnifique ?|Où puis-je trouver {TitreLivre} ?| Gatsby le Magnifique|

Pour plus d’informations, consultez l’[article de référence sur les entités pattern.Any](./reference-entity-pattern-any.md).

## <a name="machine-learned-ml-entity"></a>Entité issue du machine learning (ML)

L’entité issue du machine learning utilise le contexte pour extraire des entités en fonction des exemples étiquetés. Il s’agit de l’entité recommandée pour la création d’applications LUIS. Elle s’appuie sur des algorithmes de machine learning et nécessite que l’étiquetage soit adapté à votre application. Utilisez une entité ML pour identifier les données qui ne sont pas toujours correctement formatées, mais qui ont la même signification. 

|Exemple d’énoncé|Entité *produit* extraite|
|--|--|
|Je veux acheter un livre.|« livre »|
|Est-ce que je peux avoir ces chaussures ?|« chaussures »|
|Ajouter ce short à mon panier.|« short »|

Pour plus d’informations sur les entités issues du machine learning, [cliquez ici](./reference-entity-machine-learned-entity.md).

Pour plus d’informations, consultez l’[article de référence sur les entités issues du machine learning](./reference-entity-pattern-any.md).

#### <a name="ml-entity-with-structure"></a>Entité ML avec structure

Une entité ML peut être composée de sous-entités plus petites, chacune pouvant avoir ses propres propriétés. Par exemple, *Adresse* peut avoir la structure suivante :

* Adresse : 4567 Main Street, NY, 98052, USA
    * Numéro du bâtiment : 4567
    * Nom de la rue : Main Street
    * État : NY
    * Code postal : 98052
    * Pays : USA


## <a name="building-effective-ml-entities"></a>Création d’entités ML efficaces

Pour créer des entités issues du machine learning qui soient efficaces, suivez les bonnes pratiques ci-dessous :

* Si vous disposez d’une entité issue du machine learning avec des sous-entités, vérifiez que les différentes commandes et variantes de l’entité et des sous-entités sont présentées dans les énoncés étiquetés. Les exemples d’énoncés étiquetés doivent inclure toutes les formes valides, ainsi que les entités qui apparaissent, sont absentes et sont réorganisées au sein de l’énoncé.

* Évitez de surajuster les entités dans un ensemble très fixe. Un surajustement se produit lorsque le modèle ne généralise pas bien et qu’il s’agit d’un problème courant dans les modèles Machine Learning. Cela implique que l’application ne fonctionnera pas correctement sur les nouveaux types d’exemples. Vous devez faire varier les exemples d’énoncés étiquetés pour que l’application puisse généraliser au-delà des exemples limités que vous fournissez.

* Votre étiquetage doit être cohérent au niveau des intentions. Cela inclut même les énoncés que vous fournissez dans l’intention *Aucune*, qui incluent cette entité. Dans le cas contraire, le modèle n’est pas en mesure de déterminer les séquences efficacement.

## <a name="entities-as-features"></a>Entités en tant que fonctionnalités

Une autre chose importante à propos des entités est qu’elles peuvent être utilisées en tant que fonctionnalités ou pour distinguer les caractéristiques d’autres intentions ou entités afin que votre système puisse les observer et s’entraîner.

## <a name="entities-as-features-for-intents"></a>Entités en tant que fonctionnalités pour les intentions

Vous pouvez utiliser des entités comme un signal d’intention. Par exemple, la présence d’une certaine entité dans l’énoncé peut permettre de savoir à quelle catégorie d’intention elle appartient.

|Exemple d’énoncé|Entité|Intentionnel|
|--|--|--|
|Réserve-moi un *vol pour New York*.|City|Réserver un vol|
|Réserve-moi la *salle de conférence principale*.|Salle|Réserver une salle|

## <a name="entities-as-feature-for-entities"></a>Entités en tant que fonctionnalité pour les entités

Vous pouvez également utiliser des entités comme des indicateurs de présence d’autres entités. Un exemple courant est celui de l’utilisation d’une entité prédéfinie en tant que fonctionnalité pour une autre entité ML.
Si vous créez un système de réservation de vol et que votre énoncé ressemble à « Réserve-moi un vol Le Caire-Seattle », vous aurez les entités ML *Ville d’origine* et *Ville de destination*. Une bonne pratique consiste à utiliser l’entité prédéfinie `GeographyV2` comme une fonctionnalité pour les deux entités.

Pour plus d’informations, consultez l’[article de référence sur les entités GeographyV2](./luis-reference-prebuilt-geographyv2.md).

Vous pouvez également utiliser des entités comme des fonctionnalités obligatoires pour d’autres entités. Cela permet de résoudre les entités extraites. Par exemple, si vous créez une application de commande de pizzas et si vous disposez d’une entité ML `Size`, vous pouvez créer une entité de liste `SizeList` et l’utiliser comme une fonctionnalité obligatoire pour l’entité `Size`. Votre application retourne la valeur normalisée en tant qu’entité extraite de l’énoncé. 

Pour plus d’informations, consultez [Fonctionnalités](luis-concept-feature.md). Pour en savoir plus sur la résolution des entités prédéfinies disponibles dans votre culture, consultez [Entités prédéfinies](./luis-reference-prebuilt-entities.md). 


## <a name="entity-prediction-status-and-errors"></a>État de prédiction d’entité et erreurs

Le portail LUIS indique ce qui suit quand l’entité a une prédiction d’entité différente de celle de l’entité que vous avez étiquetée dans un exemple d’énoncé. Ce score différent est basé sur le modèle entraîné actuel. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Le portail LUIS indique quand l’entité a une prédiction d’entité différente de celle de l’entité que vous avez sélectionnée dans un exemple d’énoncé":::

Le texte qui provoque l’erreur est mis en évidence dans l’exemple d’énoncé, et l’exemple de ligne d’énoncé comporte un indicateur d’erreur à droite, sous la forme d’un triangle rouge. 

Pour résoudre les erreurs d’entité, essayez une ou plusieurs des étapes suivantes :

* Le texte mis en surbrillance est mal étiqueté. Pour résoudre ce problème, examinez l’étiquette, corrigez-la, puis réentraînez l’application. 
* Créez une [fonctionnalité](luis-concept-feature.md) pour l’entité afin d’identifier le concept de l’entité.
* Ajoutez des [exemples d’énoncés](luis-concept-utterance.md) et étiquetez-les avec l’entité.
* [Passez en revue les suggestions d’apprentissage actif](luis-concept-review-endpoint-utterances.md) pour les énoncés reçus au point de terminaison de prédiction qui peuvent aider à identifier le concept de l’entité.


## <a name="next-steps"></a>Étapes suivantes

* Découvrir de bons exemples d’[énoncés](luis-concept-utterance.md).
* Consulter [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.
* Découvrir les [limites d’application](./luis-limits.md) LUIS. 
* Suivre un [tutoriel](tutorial-machine-learned-entity.md) pour savoir comment extraire des données structurées à partir d’un énoncé à l’aide de l’entité issue du machine learning.
