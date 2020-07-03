---
title: Fonctionnalités – LUIS
description: Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 823c51f0b58481e30ff54814dde03285ad094b9e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677589"
---
# <a name="machine-learning-ml-features"></a>Caractéristiques de Machine Learning (ML)

En apprentissage automatique, une  **caractéristique** est un trait ou un attribut distinctif des données que votre système observe et dont il apprend.

Les caractéristiques de Machine Learning fournissent à LUIS des indications importantes pour rechercher les éléments qui distinguent un concept. Il ne s’agit pas de règles strictes mais de conseils que LUIS peut utiliser.  Ces conseils sont utilisés conjointement avec les étiquettes pour rechercher les données.

## <a name="what-is-a-feature"></a>Qu’est-ce qu’une caractéristique ?

Une caractéristique est un trait distinctif, qui peut être décrit comme une fonction : f(x) = y. La caractéristique est utilisée pour savoir où chercher le trait distinctif dans l’exemple d’énoncé. Lorsque vous créez votre schéma, que savez-vous de l’exemple d’énoncé qui indique le trait ? Votre réponse est votre meilleur guide pour créer des caractéristiques.

## <a name="types-of-features"></a>Types de caractéristiques

 LUIS prend en charge les listes d’expressions et d’autres modèles en tant que caractéristiques :
* Caractéristiques de liste d’expressions
* Modèle (intention ou entité) en tant que caractéristique

Les caractéristiques doivent être considérées comme une partie nécessaire de la conception de votre schéma.

## <a name="how-you-find-features-in-your-example-utterances"></a>Comment trouver des caractéristiques dans vos exemples d’énoncés

Comme LUIS est une application basée sur un langage, les caractéristiques sont basées sur du texte. Choisissez le texte qui indique le trait que vous souhaitez distinguer. Pour LUIS, la plus petite unité basée sur du texte est le jeton. Pour l’anglais, un jeton est une étendue continue (sans espaces ni ponctuation) de lettres et de chiffres. Une espace n’est pas un jeton.

Étant donné que les espaces et la ponctuation ne sont pas des jetons, privilégiez les indices textuels que vous pouvez utiliser comme caractéristiques. N’oubliez pas d’inclure les variations de mots telles que :
* formes plurielles
* temps de verbes
* abréviation
* orthographe et fautes d’orthographe

Est-ce que le texte, en tant que trait distinctif, doit :
* Mettre en correspondance un mot ou une expression exacte : envisagez d’ajouter une entité d’expression régulière ou une entité de liste comme caractéristique de l’entité ou de l’intention
* Faire correspondre un concept bien connu, tel que des dates, des heures ou des noms de personnes : utilisez une entité prédéfinie comme caractéristique de l’entité ou de l’intention
* Apprendre de nouveaux exemples au fil du temps : utilisez une liste d’expressions de quelques exemples du concept comme caractéristique de l’entité ou de l’intention

## <a name="combine-features"></a>Combiner les caractéristiques

Étant donné qu’il existe plusieurs façons de décrire un trait, vous pouvez utiliser plusieurs caractéristiques qui permettent de décrire ce trait ou ce concept. Une association courante consiste à utiliser une caractéristique de liste d’expressions et l’un des types d’entité couramment utilisés comme caractéristiques : entité prédéfinie, entité d’expression régulière ou entité de liste.

### <a name="ticket-booking-entity-example"></a>Exemple d’entité de réservation de tickets

À titre d’exemple, considérez une application permettant de réserver un vol avec une intention de réservation de vol et une entité de réservation de tickets.

L’entité de réservation de tickets est une entité issue du Machine Learning pour la destination de vol. Pour faciliter l’extraction de l’emplacement, utilisez deux caractéristiques pour vous aider :
* Liste d’expressions de mots pertinents tels que `plane`, `flight`, `reservation` ou `ticket`
* Entité `geographyV2` prédéfinie comme caractéristique de l’entité

### <a name="pizza-entity-example"></a>Exemple d’entité Pizza

Prenons un autre exemple, celui d’une application de commande de pizza avec une intention Créer une commande de pizza et une entité Pizza.

L’entité Pizza est une entité issue du Machine Learning pour les détails relatifs à la pizza. Pour faciliter l’extraction des détails, utilisez deux caractéristiques pour vous aider :
* Liste d’expressions de mots pertinents tels que `cheese`, `crust`, `pepperoni` ou `pineapple`
* Entité `number` prédéfinie comme caractéristique de l’entité

## <a name="a-phrase-list-for-a-particular-concept"></a>Liste d’expressions pour un concept particulier

Une liste d’expressions est une liste de mots ou d’expressions qui encapsule un concept particulier et qui est appliquée comme correspondance insensible à la casse au niveau du jeton.

Lorsque vous ajoutez une liste d’expressions, vous pouvez définir la caractéristique comme suit :
* **[Globale](#global-features)** . Une caractéristique globale s’applique à l’ensemble de l’application.

### <a name="when-to-use-a-phrase-list"></a>Quand utiliser une liste d’expressions

Si votre application LUIS doit être en mesure de généraliser et d’identifier de nouveaux éléments, utilisez une liste d’expressions. Les listes d’expression sont comme du vocabulaire spécifique à un domaine qui permet d’améliorer la qualité de compréhension des intentions et des entités.

### <a name="how-to-use-a-phrase-list"></a>Comment utiliser une liste d’expressions

À l’aide d’une liste d’expressions, LUIS prend en compte le contexte et généralise pour identifier les éléments qui y sont similaires, sans être une correspondance de texte exacte.

Procédure d’utilisation d’une liste d’expressions :
* Commencez par une entité de machine-learning
    * Ajouter des exemples d’énoncés
    * Étiquetez avec une entité de machine-learning
* Ajoutez une liste d’expressions.
    * Ajoutez des mots avec une signification similaire : n’ajoutez **pas** tous les mot ou toutes les expression qui vous passent par la tête. Au lieu de cela, ajoutez quelques mots ou expressions à la fois, puis effectuez à nouveau l’apprentissage et publiez.
    * Révisez le résultat et ajoutez des mots suggérés.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Scénario classique pour une liste d’expressions

Un scénario classique pour une liste d’expressions consiste à amplifier les mots liés à une idée spécifique.

Les termes médicaux constituent un bon exemple de mots nécessitant une liste d’expressions pour améliorer leur signification. Ces termes peuvent avoir une signification précise, de l’ordre du physique, du chimique, du thérapeutique... Ou totalement abstraite. Sans liste d’expressions, LUIS ignore quels termes sont importants pour votre domaine de sujet.

Si vous souhaitez extraire les termes médicaux :
* Commencez par créer des exemples d’énoncés et étiquetez les conditions médicales au sein de ces énoncés.
* Créez ensuite une liste d’expressions avec des exemples de termes dans le domaine du sujet. Cette liste d’expressions doit inclure le terme que vous avez étiqueté et d’autres termes qui décrivent le même concept.
* Ajoutez la liste d’expressions à l’entité ou à la sous-entité qui extrait le concept utilisé dans la liste d’expressions. Le scénario le plus courant est un composant (enfant) d’une entité de machine-learning. Si la liste d’expressions doit être appliquée à toutes les intentions ou entités, marquez la liste d’expressions comme une liste d’expressions globale. L’indicateur `enabledForAllModels` contrôle l’étendue de ce modèle dans l’API.

### <a name="token-matches-for-a-phrase-list"></a>Correspondances de jeton pour une liste d’expressions

Une liste d’expressions s’applique au niveau du jeton, quelle que soit la casse. Le tableau suivant montre comment une liste d’expressions contenant le mot `Ann` est appliquée aux variations des mêmes caractères dans cet ordre.


| Variation de jeton de `Ann` | Liste d’expressions correspondant à la détection du jeton |
|--------------------------|---------------------------------------|
| ANN<br>aNN<br>           | Oui : le jeton est `Ann`                  |
| Ann’s                    | Oui : le jeton est `Ann`                  |
| Anne                     | Non : le jeton est `Anne`                  |


<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Un modèle en tant que caractéristique renforce un autre modèle

Vous pouvez ajouter un modèle (intention ou entité) en tant que caractéristique à un autre modèle (intention ou entité). En ajoutant une intention ou une entité existante en tant que caractéristique, vous ajoutez un concept bien défini avec des exemples étiquetés.

Lorsque vous ajoutez un modèle en tant que caractéristique, vous pouvez définir la caractéristique comme suit :
* **[Requise](#required-features)** . Une caractéristique requise doit être trouvée afin que le modèle soit retourné à partir du point de terminaison de prédiction.
* **[Globale](#global-features)** . Une caractéristique globale s’applique à l’ensemble de l’application.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quand utiliser une entité comme caractéristique d’une intention

Ajoutez une entité en tant que caractéristique d’une intention lorsque la détection de cette entité est significative pour l’intention.

Par exemple, si l’intention vise à réserver un vol, `BookFlight`, et que l’entité désigne les informations du billet (telles que le nombre de sièges, l’origine et la destination), trouver l’entité d’informations du billet doit alors ajouter du poids à la prédiction de l’intention `BookFlight`.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quand utiliser une entité comme caractéristique d’une autre entité

Une entité (A) doit être ajoutée en tant que caractéristique à une autre entité (B) lorsque la détection de cette entité (A) est importante pour la prédiction de l’entité (B).

Par exemple, si n entités d’adresse de livraison contenaient une sous-entité d’adresse de rue, la recherche de la sous-entité d’adresse de rue ajoute un poids significatif à la prédiction pour l’entité d’adresse de livraison.

* Adresse de livraison (entité issue du Machine Learning)
    * Numéro de rue (sous-entité)
    * Rue (sous-entité)
    * Ville (sous-entité)
    * Région ou département (sous-entité)
    * Pays/région (sous-entité)
    * Code postal (sous-entité)

## <a name="nested-subentities-with-features"></a>Sous-entités imbriquées avec des caractéristiques

Une sous-entité issue du machine learning indique la présence d’un concept pour l’entité parente, que celle-ci soit une autre sous-entité ou une entité supérieure. La valeur de la sous-entité fait office de caractéristique pour son entité parente.

Une sous-entité peut avoir pour caractéristique tant une liste d’expressions qu’un modèle (une autre entité).

Quand la sous-entité comprend une liste d’expressions, cela améliore le vocabulaire du concept, mais n’ajoute pas d’informations à la réponse JSON de la prédiction.

Lorsque la sous-entité a une caractéristique d’une autre entité, la réponse JSON inclut les données extraites de celle-ci.

## <a name="required-features"></a>Caractéristiques requises

Une caractéristique requise doit être trouvée pour que le modèle soit retourné à partir du point de terminaison de prédiction. Utilisez une caractéristique requise lorsque vous savez que vos données entrantes doivent correspondre à la caractéristique.

Si le texte de l’énoncé ne correspond pas à la caractéristique requise, il n’est pas extrait.

**Une caractéristique requise utilise une entité non issue du Machine Learning** :
* Entité d’expression régulière
* Entité de liste
* Entité prédéfinie

Quelles caractéristiques faut-il définir comme « requises » ? Si vous êtes sûr que votre modèle sera révélé par les données, définissez la caractéristique comme « requise ». Une caractéristique requise ne retourne rien si elle est introuvable.

Poursuivons avec l’exemple de l’adresse de livraison :
* Adresse de livraison (entité issue du Machine Learning)
    * Numéro de rue (sous-entité)
    * Rue (sous-entité)
    * Numéro de rue (sous-entité)
    * Ville (sous-entité)
    * Région ou département (sous-entité)
    * Pays/région (sous-entité)
    * Code postal (sous-entité)

### <a name="required-feature-using-prebuilt-entities"></a>Caractéristique requise utilisant des entités prédéfinies

Les informations de localité/ville, département/province et pays/région sont généralement constitués d’un ensemble fermé de listes qui ne changent pas beaucoup au fil du temps. Ces entités peuvent avoir des caractéristiques recommandées pertinentes et ces caractéristiques peuvent être définies comme « requises ». Cela signifie que l’intégralité de l’adresse de livraison n’est pas renvoyée, car les entités avec les caractéristiques requises sont introuvables.

Que se passe-t-il si les informations de localité/ville, département/province et pays/région figurent dans l’énoncé, mais sont exprimées dans un dialecte ou un argot hermétique que LUIS ne comprend pas ? Si vous souhaitez utiliser un post-traitement pour aider à résoudre l’entité (par exemple, si LUIS émet un score de confiance faible), ne définissez pas la caractéristique comme « requise ».

Un autre exemple de caractéristique requise pour l’adresse de livraison consiste à définir le numéro de rue en tant que numéro [prédéfini](luis-reference-prebuilt-entities.md) requis. Cela permet à un utilisateur d’entrer « 1 rue Microsoft » ou « un rue Microsoft ». Ces deux options utiliseront la forme numérique « 1 » pour la sous-entité du numéro de rue.

### <a name="required-feature-using-list-entities"></a>Caractéristique requise utilisant des entités de listes

Une [entité de liste](reference-entity-list.md) est utilisée en tant que liste de noms canoniques, ainsi que leurs synonymes. En tant que caractéristique requise, si l’énoncé n’inclut ni le nom canonique ni un synonyme, l’entité n’est pas retournée dans le cadre du point de terminaison de prédiction.

Continuons avec notre exemple d’adresse de livraison. Supposons que votre entreprise n’expédie que vers un ensemble limité de pays/régions. Vous pouvez créer une entité de liste qui comprend plusieurs méthodes utilisables par votre client pour référencer le pays. Si LUIS ne trouve pas de correspondance exacte dans le texte de l’énoncé, l’entité (qui a la caractéristique requise de l’entité de liste) n’est pas renvoyée dans la prédiction.

|Nom canonique|Synonymes|
|--|--|
|États-Unis|États-Unis<br>É.-U. A.<br>US<br>États-Unis<br>0|

L’application cliente, par exemple, un robot conversationnel, peut poser une question subsidiaire permettant au client de comprendre que la sélection du pays/région est limitée et _obligatoire_.

### <a name="required-feature-using-regular-expression-entities"></a>Caractéristique requise utilisant des entités d’expression régulière

Une [entité d’expression régulière](reference-entity-regular-expression.md) utilisée comme caractéristique requise fournit des caractéristiques de recherche en texte enrichi.

Toujours dans notre exemple d’adresse de livraison, vous pouvez créer une expression régulière qui capture les règles de syntaxe des codes postaux de pays/régions.

## <a name="global-features"></a>Caractéristiques globales

Bien que l’utilisation la plus courante consiste à appliquer une caractéristique à un modèle spécifique, vous pouvez configurer la caractéristique en tant que **caractéristique globale** pour l’appliquer à l’ensemble de votre application.

L’utilisation la plus courante d’une caractéristique globale consiste à ajouter un vocabulaire supplémentaire, tel que des mots d’une autre langue, à l’application. Si vos clients utilisent une langue principale, mais s’attendent à pouvoir utiliser une autre langue dans le même énoncé, vous pouvez ajouter une caractéristique qui comprend des mots de la langue secondaire.

Étant donné que l’utilisateur s’attend à utiliser la langue secondaire pour toute intention ou entité, elle doit être ajoutée dans une liste d’expressions avec la liste d’expressions configurée en tant que caractéristique globale.

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* [Étendre](schema-change-prediction-runtime.md) vos modèles d’application au niveau du runtime de prédiction
* Consultez [Ajouter des fonctionnalités](luis-how-to-add-features.md) pour découvrir comment ajouter des fonctionnalités à votre application LUIS.
