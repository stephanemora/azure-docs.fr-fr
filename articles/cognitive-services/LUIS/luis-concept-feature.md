---
title: Fonctionnalités – LUIS
description: Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 906876e39eb7ff31c2e6b954d1514d8afc50bf3a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591894"
---
# <a name="machine-learning-ml-features"></a>Caractéristiques de Machine Learning (ML)

En Machine Learning, une  **caractéristique**  (« feature ») est un trait ou un attribut distinctif des données observées par le système.

Les caractéristiques de Machine Learning fournissent à LUIS des indications importantes pour rechercher les éléments qui distinguent un concept. Il ne s’agit pas de règles strictes mais de conseils que LUIS peut utiliser.  Ces conseils sont utilisés conjointement avec les étiquettes pour rechercher les données.

 LUIS prend en charge les listes d’expressions et l’utilisation d’autres entités en tant que caractéristiques :
* Caractéristiques de liste d’expressions
* Modèle (intention ou entité) en tant que caractéristique

Les caractéristiques doivent être considérées comme une partie nécessaire de la conception de votre schéma.

## <a name="a-phrase-list-for-a-particular-concept"></a>Liste d’expressions pour un concept particulier

Une liste d’expressions est une liste de mots ou d’expressions qui encapsule un concept particulier.

Lorsque vous ajoutez une liste d’expressions, vous pouvez définir la caractéristique comme suit :
* **[Globale](#global-features)** . Une caractéristique globale s’applique à l’ensemble de l’application.

### <a name="when-to-use-a-phrase-list"></a>Quand utiliser une liste d’expressions

Si votre application LUIS doit être en mesure de généraliser et d’identifier de nouveaux éléments, utilisez une liste d’expressions. Les listes d’expression sont comme du vocabulaire spécifique à un domaine qui permet d’améliorer la qualité de compréhension des intentions et des entités.

### <a name="how-to-use-a-phrase-list"></a>Comment utiliser une liste d’expressions

À l’aide d’une liste d’expressions, LUIS prend en compte le contexte et généralise pour identifier les éléments qui y sont similaires, sans être une correspondance de texte exacte.

Procédure d’utilisation d’une liste d’expressions :
* Commencez par une entité issue du Machine Learning.
    * Ajoutez des exemples d’énoncés.
    * Étiquetez avec une entité issue du Machine Learning.
* Ajoutez une liste d’expressions.
    * Ajoutez des mots avec une signification similaire : n’ajoutez **pas** tous les mot ou toutes les expression qui vous passent par la tête. Au lieu de cela, ajoutez quelques mots ou expressions à la fois, puis effectuez à nouveau l’apprentissage et publiez.
    * Révisez le résultat et ajoutez des mots suggérés.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Scénario classique pour une liste d’expressions

Un scénario classique pour une liste d’expressions consiste à amplifier les mots liés à une idée spécifique.

Les termes médicaux constituent un bon exemple de mots nécessitant une liste d’expressions pour améliorer leur signification. Ces termes peuvent avoir une signification précise, de l’ordre du physique, du chimique, du thérapeutique... Ou totalement abstraite. Sans liste d’expressions, LUIS ignore quels termes sont importants pour votre domaine de sujet.

Si vous souhaitez extraire les termes médicaux :
* Commencez par créer des exemples d’énoncés et étiquetez les conditions médicales au sein de ces énoncés.
* Créez ensuite une liste d’expressions avec des exemples de termes dans le domaine du sujet. Cette liste d’expressions doit inclure le terme que vous avez étiqueté et d’autres termes qui décrivent le même concept.
* Ajoutez la liste d’expressions à l’entité ou à la sous-entité qui extrait le concept utilisé dans la liste d’expressions. Le scénario le plus courant est un composant (enfant) d’une entité issue du Machine Learning. Si la liste d’expressions doit être appliquée à toutes les intentions ou entités, marquez la liste d’expressions comme une liste d’expressions globale. L’indicateur `enabledForAllModels` contrôle l’étendue de ce modèle dans l’API.

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
    * Pays (sous-entité)
    * Code postal (sous-entité)

## <a name="required-features"></a>Caractéristiques requises

Une caractéristique requise doit être trouvée pour que le modèle soit retourné à partir du point de terminaison de prédiction. Utilisez une caractéristique requise lorsque vous savez que vos données entrantes doivent correspondre à la caractéristique.

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
    * Pays (sous-entité)
    * Code postal (sous-entité)

### <a name="required-feature-using-prebuilt-entities"></a>Caractéristique requise utilisant des entités prédéfinies

La ville, la région et le pays sont généralement un ensemble de listes fermé, ce qui signifie qu’elles ne changent pas beaucoup au fil du temps. Ces entités peuvent avoir des caractéristiques recommandées pertinentes et ces caractéristiques peuvent être définies comme « requises ». Cela signifie que l’intégralité de l’adresse de livraison n’est pas renvoyée, car les entités avec les caractéristiques requises sont introuvables.

Que se passe-t-il si la ville, la région ou le pays se trouvent dans l’énoncé, mais sous la forme d’une localité ou d’un mot d’argot que LUIS ne pourra pas appréhender ? Si vous souhaitez utiliser un post-traitement pour aider à résoudre l’entité (par exemple, si LUIS émet un score de confiance faible), ne définissez pas la caractéristique comme « requise ».

Un autre exemple de caractéristique requise pour l’adresse de livraison consiste à définir le numéro de rue en tant que numéro [prédéfini](luis-reference-prebuilt-entities.md) requis. Cela permet à un utilisateur d’entrer « 1 rue Microsoft » ou « un rue Microsoft ». Ces deux options utiliseront la forme numérique « 1 » pour la sous-entité du numéro de rue.

### <a name="required-feature-using-list-entities"></a>Caractéristique requise utilisant des entités de listes

Une [entité de liste](reference-entity-list.md) est utilisée en tant que liste de noms canoniques, ainsi que leurs synonymes. En tant que caractéristique requise, si l’énoncé n’inclut ni le nom canonique ni un synonyme, l’entité n’est pas retournée dans le cadre du point de terminaison de prédiction.

Continuons avec notre exemple d’adresse de livraison. Supposons que votre entreprise soit livrée uniquement dans certains pays. Vous pouvez créer une entité de liste qui comprend plusieurs méthodes utilisables par votre client pour référencer le pays. Si LUIS ne trouve pas de correspondance exacte dans le texte de l’énoncé, l’entité (qui a la caractéristique requise de l’entité de liste) n’est pas renvoyée dans la prédiction.

|Nom canonique|Synonymes|
|--|--|
|États-Unis|É.-U.<br>É.-U. A.<br>US<br>USA<br>0|

L’application cliente, telle qu’un chatbot, peut poser une question de suivi, de sorte que le client comprend que la sélection du pays est limitée et _requise_.

### <a name="required-feature-using-regular-expression-entities"></a>Caractéristique requise utilisant des entités d’expression régulière

Une [entité d’expression régulière](reference-entity-regular-expression.md) utilisée comme caractéristique requise fournit des caractéristiques de recherche en texte enrichi.

Toujours dans notre exemple d’adresse de livraison : vous pouvez créer une expression régulière qui capture les règles de syntaxe des codes postaux des pays.

## <a name="global-features"></a>Caractéristiques globales

Bien que l’utilisation la plus courante consiste à appliquer une caractéristique à un modèle spécifique, vous pouvez configurer la caractéristique en tant que **caractéristique globale** pour l’appliquer à l’ensemble de votre application.

L’utilisation la plus courante d’une caractéristique globale consiste à ajouter un vocabulaire supplémentaire, tel que des mots d’une autre langue, à l’application. Si vos clients utilisent une langue principale, mais s’attendent à pouvoir utiliser une autre langue dans le même énoncé, vous pouvez ajouter une caractéristique qui comprend des mots de la langue secondaire.

Étant donné que l’utilisateur s’attend à utiliser la langue secondaire pour toute intention ou entité, elle doit être ajoutée dans une liste d’expressions avec la liste d’expressions configurée en tant que caractéristique globale.

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* [Étendre](schema-change-prediction-runtime.md) vos modèles d’application au niveau du runtime de prédiction
* Consultez [Ajouter des fonctionnalités](luis-how-to-add-features.md) pour découvrir comment ajouter des fonctionnalités à votre application LUIS.
