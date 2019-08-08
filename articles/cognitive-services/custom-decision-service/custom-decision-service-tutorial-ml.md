---
title: 'Didacticiel : Caractérisation et spécification de caractéristiques - Service Décision personnalisée'
titlesuffix: Azure Cognitive Services
description: Didacticiel sur la personnalisation et ma définition des caractéristiques de Machine Learning dans le Service Décision personnalisée.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 409dcf7dc224eaf2a3f51325010507b9182fadf2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707045"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Didacticiel : Caractérisation et spécification de caractéristiques

Ce didacticiel traite des fonctionnalités Machine Learning avancées du Service Décision personnalisée. Le didacticiel compose de deux parties : [personnalisation](#featurization-concepts-and-implementation) et [spécification de fonctionnalité](#feature-specification-format-and-apis). La personnalisation fait référence à la représentation de vos données en tant que « fonctionnalités » pour Machine Learning. La spécification de fonctionnalité traite du format JSON et des API auxiliaires pour spécifier des fonctionnalités.

Par défaut, Machine Learning dans le Service Décision personnalisée est transparent pour le client. Les fonctionnalités sont automatiquement extraites de votre contenu, et un algorithme d’apprentissage par renforcement standard est utilisé. L’extraction de caractéristiques utilise plusieurs autres API Azure Cognitive Services : [Liaison d’entités](../entitylinking/home.md), [Analyse de texte](../text-analytics/overview.md), [Émotion](../emotion/home.md) et [Vision par ordinateur](../computer-vision/home.md). Ce didacticiel peut être ignoré si seule la fonctionnalité par défaut est utilisée.

## <a name="featurization-concepts-and-implementation"></a>Personnalisation : concepts et implémentation

Le Service Décision personnalisée prend des décisions à raison d’une à la fois. Chaque décision implique de choisir entre plusieurs alternatives, à savoir des actions. Selon l’application, la décision peut choisir une action unique ou une liste (courte) classée d’actions.

Par exemple, la personnalisation de la sélection d’articles sur la première page d’un site web. Ici, les actions correspondent à des articles, et chaque décision correspond aux articles à afficher pour un utilisateur donné.

Chaque action est représentée par un vecteur de propriétés, désormais appelé *fonctionnalités*. Vous pouvez spécifier de nouvelles fonctionnalités en plus des fonctionnalités extraites automatiquement. Vous pouvez également demander au Service Décision personnalisée de consigner certaines fonctionnalités, mais de les ignorer pour Machine Learning.

### <a name="native-vs-internal-features"></a>Fonctionnalités natives et internes

Vous pouvez spécifier des fonctionnalités dans un format plus naturel pour votre application, qu’il s’agisse d’un nombre, d’une chaîne ou d’un tableau. Ces fonctionnalités sont appelées des « fonctionnalités natives ». Le Service Décision personnalisée traduit chaque fonctionnalité native en une ou plusieurs fonctionnalités numériques, appelées des « fonctionnalités internes ».

La traduction en fonctionnalités internes est la suivante :

- les fonctionnalités numériques ne changent pas.
- un tableau numérique se traduit par plusieurs fonctionnalités numériques, une pour chaque élément du tableau.
- une fonctionnalité à valeur de chaîne `"Name":"Value"` est traduite par défaut en une fonctionnalité avec le nom `"NameValue"` et la valeur 1.
- Si vous le souhaitez, une chaîne peut être représentée comme [bag-of-words](https://en.wikipedia.org/wiki/Bag-of-words_model). Une fonctionnalité interne est ensuite créée pour chaque mot de la chaîne, dont la valeur correspond au nombre d’occurrences de ce mot.
- Les fonctionnalités internes à valeur nulle sont ignorées.

### <a name="shared-vs-action-dependent-features"></a>Fonctionnalités partagées et dépendantes de l’action

Certaines fonctionnalités font référence à la décision entière et sont les mêmes pour toutes les actions. Nous les appelons des *fonctionnalités partagées*. Certaines autres fonctionnalités sont spécifiques à une action particulière. Nous les appelons des *fonctionnalités dépendantes de l’action* (ADF).

Dans l’exemple exécuté, les fonctionnalités partagées peuvent décrire l’utilisateur et/ou l’état du monde. Fonctionnalités comme la géolocalisation, l’âge et le sexe de l’utilisateur, et les principaux événements qui sont produisent maintenant. Les fonctionnalités dépendantes de l’action peuvent décrire les propriétés d’un article donné, comme les sujets abordés dans cet article.

### <a name="interacting-features"></a>Fonctionnalités d’interaction

Les fonctionnalités « interagissent » souvent : l’effet de l’une dépend des autres. Par exemple, la fonctionnalité X indique si l’utilisateur est intéressé par le sport. La fonctionnalité Y indique si un article donné parle de sport. Ainsi, l’effet de la fonctionnalité Y dépend fortement de la fonctionnalité X.

Pour prendre en compte l’interaction entre les fonctionnalités X et Y, créez une fonctionnalité *quadratique* dont la valeur est X\*Y. (Nous parlons également de « croisement » entre X et Y.) Vous pouvez choisir les paires de fonctionnalités qui se croisent.

> [!TIP]
> Une fonctionnalité partagée doit être croisée par des fonctionnalités dépendantes de l’action afin d’influencer son ordre de priorité. Une fonctionnalité dépendante de l’action doit être croisée par des fonctionnalités partagées pour pouvoir contribuer à la personnalisation.

En d’autres termes, une fonctionnalité partagée non croisée par des ADF influence chaque action de la même manière. Une ADF non croisée par des fonctionnalités partagées influence chaque décision également. Ces types de fonctionnalités peuvent réduire l’écart d’estimations de récompense.

### <a name="implementation-via-namespaces"></a>Implémentation via des espaces de noms

Vous pouvez implémenter des fonctionnalités croisées (ainsi que d’autres concepts de personnalisation) via la « ligne de commande VW » sur le portail. La syntaxe est basée sur la ligne de commande [Vowpal Wabbit](http://hunch.net/~vw/).

Le concept d’*espace de noms*, un sous-ensemble de fonctionnalités, est au cœur de la personnalisation. Chaque fonctionnalité n’appartient qu’à un seul espace de noms. L’espace de noms peut être spécifié explicitement lorsque la valeur de la fonctionnalité est fournie au Service Décision personnalisée. C’est la seule méthode possible pour faire référence à une fonctionnalité dans la ligne de commande VW.

Un espace de noms est « partagé » ou « dépendant de l’action » : il ne comprend que des fonctionnalités partagées, ou ne comprend que des fonctionnalités dépendantes de l’action pour la même action.

> [!TIP]
> Il est recommandé d’encapsuler des fonctionnalités dans des espaces de noms spécifiés explicitement. Regroupez les fonctionnalités associées dans le même espace de noms.

Si l’espace de noms n’est pas fourni, la fonctionnalité est automatiquement assignée à l’espace de noms par défaut.

> [!IMPORTANT]
> Les fonctionnalités et les espaces de noms ne doivent pas nécessairement être cohérents entre les actions. Plus particulièrement, un espace de noms peut avoir des fonctionnalités différentes pour différentes actions. En outre, un espace de noms donné peut être défini pour certaines actions et pas pour d’autres.

Plusieurs fonctionnalités internes provenant de la même fonctionnalité native à valeur de chaîne sont regroupées dans le même espace de noms. Deux fonctionnalités natives se trouvant dans des espaces de noms différents sont considérées comme distinctes, même si elles ont le même nom de fonctionnalité.

> [!IMPORTANT]
> Bien que les ID d’espaces de noms longs et descriptifs soient courants, la ligne de commande VW ne distingue pas les espaces de noms dont l’ID commence par la même lettre. Dans ce qui suit, les ID d’espaces de noms sont des lettres uniques, telles que `x` et `y`.

Les détails d’implémentation sont les suivants :

- Pour croiser les espaces de noms `x` et `y`, écrivez `-q xy` ou `--quadratic xy`. Chaque fonctionnalité dans `x` est ensuite croisée avec chaque fonctionnalité dans `y`. Utilisez `-q x:` pour croiser `x` avec chaque espace de noms, et `-q ::` pour croiser toutes les paires d’espaces de noms.

- Pour ignorer toutes les fonctionnalités dans l’espace de noms `x`, écrivez `--ignore x`.

Ces commandes s’appliquent à chaque action séparément si les espaces de noms sont définis.

### <a name="estimated-average-as-a-feature"></a>Moyenne estimée en tant que fonctionnalité

À titre d’expérience, quelle serait la récompense moyenne d’une action donnée si elle était choisie pour toutes les décisions ? Cette récompense moyenne peut servir de mesure de la « qualité globale » de cette action. On ne sait pas exactement si d’autres actions ont été choisies dans certaines décisions. Ceci peut toutefois être estimé à l’aide de techniques d’apprentissage par renforcement. La qualité de cette estimation s’améliore généralement au fil du temps.

Vous pouvez choisir d’inclure cette « récompense moyenne estimée » en tant que fonctionnalité pour une action donnée. Le Service Décision personnalisée met alors automatiquement à jour cette estimation à mesure que de nouvelles données arrivent. Cette fonctionnalité est appelée la *fonctionnalité marginale* de cette action. Des fonctionnalités marginales peuvent être utilisées pour Machine Learning et l’audit.

Pour ajouter des fonctionnalités marginales, écrivez `--marginal <namespace>` dans la ligne de commande VW. Définissez `<namespace>` dans JSON comme suit :

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Insérez cet espace de noms, ainsi que d’autres fonctionnalités dépendantes de l’action d’une action donnée. Fournissez cette définition de chaque décision, en utilisant les mêmes `mf_name` et `action_id` pour toutes les décisions.

La fonctionnalité marginale est ajoutée à chaque action avec `<namespace>`. `action_id` peut être n’importe quel nom de fonctionnalité qui identifie l’action de manière unique. Le nom de la fonctionnalité est défini sur `mf_name`. Plus particulièrement, les fonctionnalités marginales avec différents `mf_name` sont considérées comme des fonctionnalités différentes, un poids différent est appris pour chaque `mf_name`.

L’utilisation par défaut réside dans le fait que `mf_name` est identique pour toutes les actions. Un poids est donc appris pour toutes les fonctionnalités marginales.

Vous pouvez également spécifier plusieurs fonctionnalités marginales pour la même action, avec les mêmes valeurs, mais des noms de fonctionnalités différents.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-encodage à chaud

Vous pouvez choisir de représenter certaines fonctionnalités comme des vecteurs de bits, où chaque bit correspond à une plage de valeurs possibles. Ce bit est défini sur 1 si et seulement si la fonctionnalité se trouve dans cette plage. Ainsi, il existe un bit « chaud » qui est défini sur 1, et les autres sont définis sur 0. Cette représentation est communément appelée *1-encodage à chaud*.

1-encodage à chaud est généralement utilisé pour les fonctionnalités catégorielles telles que « région géographique » qui n’ont pas de représentation numérique explicite. Il est également recommandé pour les fonctionnalités numériques dont l’influence sur la récompense est susceptible d’être non linéaire. Par exemple, un article donné peut être pertinent pour un groupe d’âge particulier et non pertinent pour toute personne plus âgée ou plus jeune.

Une fonctionnalité à valeur de chaîne est encodée à chaud (1) par défaut : une fonctionnalité interne distincte est créée pour chaque valeur possible. L’encodage à chaud (1) automatique de fonctionnalités numériques et/ou de plages personnalisées n’est actuellement pas fourni.

> [!TIP]
> Les algorithmes de Machine Learning traitent toutes les valeurs possibles d’une fonctionnalité interne donnée de manière uniforme : à l’aide d’un « poids » commun. L’encodage à chaud (1) autorise un « poids » distinct pour chaque plage de valeurs. Des plages plus petites permettent d’obtenir de meilleures récompenses lorsque des données suffisantes sont collectées, mais peuvent augmenter la quantité de données nécessaire pour disposer de meilleures récompenses.

## <a name="feature-specification-format-and-apis"></a>Spécification de fonctionnalité : format et API

Vous pouvez spécifier des fonctionnalités via plusieurs API auxiliaires. Toutes les API utilisent un format JSON commun. Vous trouverez ci-dessous les API et le format à un niveau conceptuel. La spécification est complétée par un schéma Swagger.

Le modèle JSON de base pour la spécification de fonctionnalité est le suivant :

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Ici, `<name>` et `<value>` correspondent au nom de fonctionnalité et à la valeur de fonctionnalité, respectivement. `<value>` peut être une chaîne, un entier, une valeur flottante, une valeur booléenne ou un tableau. Une fonctionnalité non encapsulée dans un espace de noms est automatiquement assignée à l’espace de noms par défaut.

Pour représenter une chaîne comme « bag-of-words », utilisez une syntaxe spéciale `"_text":"string"` au lieu de `"<name>":<value>`. En effet, une fonctionnalité interne distincte est créée pour chaque mot dans la chaîne. Sa valeur correspond au nombre d’occurrences de ce mot.

Si `<name>` commence par « _ » (et non `"_text"`), la fonctionnalité est alors ignorée.

> [!TIP]
> Vous fusionnez parfois des fonctionnalités de plusieurs sources JSON. Pour plus de commodité, vous pouvez les représenter comme suit :
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Ici, `<features>` fait référence à la spécification de fonctionnalité de base définie précédemment. Des niveaux plus profonds d’« imbrication » sont également autorisés. Le Service Décision personnalisée recherche automatiquement les objets JSON « les plus profonds » pouvant être interprétés comme `<features>`.

#### <a name="feature-set-api"></a>API d’ensemble des fonctionnalités

L’API d’ensemble de fonctionnalités retourne une liste des fonctionnalités au format JSON décrit précédemment. Vous pouvez utiliser plusieurs points de terminaison d’API d’ensemble de fonctionnalités. Chaque point de terminaison est identifié par un ID et une URL d’ensemble de fonctionnalités. Le mappage entre les ID et URL d’ensemble de fonctionnalités est défini sur le portail.

Appelez l’API d’ensemble de fonctionnalités en insérant l’ID d’ensemble de fonctionnalités correspondant à l’emplacement approprié dans JSON. Pour les fonctionnalités dépendantes de l’action, l’appel est automatiquement paramétré par l’ID d’action. Vous pouvez spécifier plusieurs ID d’ensemble de fonctionnalités pour la même action.

#### <a name="action-set-api-json-version"></a>API d’ensemble d’actions (version JSON)

L’API d’ensemble d’actions comporte une version dans laquelle les actions et les fonctionnalités sont spécifiées dans JSON. Les fonctionnalités peuvent être spécifiées explicitement et/ou via des API d’ensemble de fonctionnalités. Les fonctionnalités partagées peuvent être spécifiées une seule fois pour toutes les actions.

#### <a name="ranking-api-http-post-call"></a>API de classement (appel HTTP POST)

L’API de classement comporte une version qui utilise l’appel HTTP POST. Le corps de cet appel spécifie les actions et les fonctionnalités via une syntaxe JSON flexible.

Les actions peuvent être spécifiées explicitement et/ou via des API d’ensemble d’actions. Chaque fois qu’un ID d’ensemble d’actions est rencontré, un appel vers le point de terminaison d’API d’ensemble d’actions correspondante est exécuté.

Comme pour l’API d’ensemble d’actions, les fonctionnalités peuvent être spécifiées explicitement et/ou via des API d’ensemble de fonctionnalités. Les fonctionnalités partagées peuvent être spécifiées une seule fois pour toutes les actions.
