---
title: Interroger le graphe de jumeaux
titleSuffix: Azure Digital Twins
description: Découvrez comment interroger le graphe de jumeaux Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9d8a72f4457c6759dc93fcdeae03abd1d4c8b168
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729828"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Interroger le graphe de jumeaux Azure Digital Twins

Cet article fournit des exemples de requête et des instructions sur l’utilisation du **langage de requête Azure Digital Twins** pour interroger votre [graphe de jumeaux](concepts-twins-graph.md) afin d’obtenir des informations. (Pour une présentation du langage de requête, consultez [Langage de requête](concepts-query-language.md).)

L’article contient des exemples de requête qui illustrent la structure du langage de requête et les opérations de requête courantes pour des jumeaux numériques. Il décrit également comment exécuter vos requêtes une fois que vous les avez écrites, avec l’[API de requête](/rest/api/digital-twins/dataplane/query) ou un [SDK](concepts-apis-sdks.md#overview-data-plane-apis) Azure Digital Twins.

> [!NOTE]
> Si vous exécutez les exemples de requête ci-dessous avec un appel d’API ou de SDK, vous devez condenser le texte de la requête sur une seule ligne.

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="show-all-digital-twins"></a>Montrer tous les jumeaux numériques

Voici la requête de base qui retourne la liste de tous les jumeaux numériques dans l’instance :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Requête par propriété

Obtenir les jumeaux numériques d’après leurs **propriétés** (y compris l’ID et les métadonnées) :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty1":::

Comme le montre la requête ci-dessus, l’ID d’un jumeau numérique est interrogé à l’aide du champ de métadonnées `$dtId`.

>[!TIP]
> Si vous utilisez Cloud Shell pour exécuter une requête avec des champs de métadonnées qui commencent par `$`, vous devez placer le `$` dans une séquence d’échappement avec un accent grave pour signaler à Cloud Shell qu’il ne s’agit pas d’une variable et qu’il doit être consommé comme littéral dans le texte de la requête.

Vous pouvez également obtenir des jumeaux en fonction de **la définition ou non d’une certaine propriété**. Voici une requête qui récupère les jumeaux dont la propriété *Location* a été définie :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty2":::

Cette requête vous permet d’obtenir des jumeaux par le biais de leurs propriétés *tag*, comme décrit dans [Ajouter des étiquettes à des jumeaux numériques](how-to-use-tags.md). Voici une requête qui récupère tous les jumeaux étiquetés avec *red* :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

Vous pouvez également obtenir des jumeaux selon le **type d’une propriété**. Voici une requête qui récupère les jumeaux dont la propriété *Temperature* est un nombre :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty3":::

>[!TIP]
> Si une propriété est de type `Map`, vous pouvez utiliser les clés et valeurs de la carte directement dans la requête, comme suit :
> :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Requête par modèle

L’opérateur `IS_OF_MODEL` peut être utilisé pour filtrer en fonction du [modèle](concepts-models.md) de jumeau.

Il prend en compte l’[héritage](concepts-models.md#model-inheritance) et la [gestion des versions](how-to-manage-model.md#update-models) de modèle, et prend la valeur **true** pour un jumeau donné si le jumeau remplit l’une des conditions suivantes :

* Le jumeau implémente directement le modèle fourni à `IS_OF_MODEL()`, et le numéro de version du modèle sur le jumeau est *supérieur ou égal* au numéro de version du modèle fourni.
* Le jumeau implémente un modèle qui *étend* le modèle fourni à `IS_OF_MODEL()`, et le numéro de version du modèle étendu du jumeau est *supérieur ou égal* au numéro de version du modèle fourni.

Par exemple, si vous interrogez les jumeaux du modèle `dtmi:example:widget;4`, la requête retourne tous les jumeaux basés sur la **version 4 ou ultérieure** du modèle **widget**, ainsi que les jumeaux basés sur la version **4 ou ultérieure** de tous les **modèles qui héritent de widget**.

`IS_OF_MODEL` peut prendre plusieurs paramètres différents, et le reste de cette section est dédié à ses différentes options de surcharge.

L’utilisation la plus simple de `IS_OF_MODEL` ne prend qu’un paramètre `twinTypeName` : `IS_OF_MODEL(twinTypeName)`.
Voici un exemple de requête qui transmet une valeur dans ce paramètre :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel1":::

Pour spécifier une collection de jumeaux à rechercher lorsqu’il en existe plusieurs (par exemple, lorsqu’un `JOIN` est utilisé), ajoutez le paramètre `twinCollection` : `IS_OF_MODEL(twinCollection, twinTypeName)`.
Voici un exemple de requête qui transmet une valeur dans ce paramètre :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel2":::

Pour effectuer une correspondance exacte, ajoutez le paramètre `exact` : `IS_OF_MODEL(twinTypeName, exact)`.
Voici un exemple de requête qui transmet une valeur dans ce paramètre :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel3":::

Vous pouvez également passer les trois arguments ensemble : `IS_OF_MODEL(twinCollection, twinTypeName, exact)`.
Voici un exemple de requête spécifiant une valeur pour les trois paramètres :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Requête par relation

Lorsque vous effectuez une interrogation basée sur les **relations** des jumeaux numériques, sachez que le langage du magasin de requêtes d’Azure Digital Twins présente une syntaxe spéciale.

Les relations sont tirées (pull) puis ajoutées à l’étendue de la requête dans la clause `FROM`. Contrairement aux langages de type SQL « classiques », chaque expression de cette clause `FROM` n’est pas une table. La clause `FROM` exprime plutôt un parcours des relations entre les entités. Pour parcourir les relations, Azure Digital Twins utilise une version personnalisée de `JOIN`.

Rappelez-vous qu’avec les capacités de [modèle](concepts-models.md) d’Azure Digital Twins, les relations n’existent pas indépendamment des jumeaux, ce qui signifie que les relations ici ne peuvent être interrogées indépendamment et doivent être liées à un jumeau.
Pour refléter ce fait, le mot clé `RELATED` est utilisé dans la clause `JOIN` pour extraire l’ensemble d’un certain type de relation provenant de la collection de jumeaux. La requête doit ensuite filtrer dans la clause `WHERE` pour indiquer le(s) jumeau(x) spécifique(s) à utiliser dans la requête de relation (à l’aide des valeurs `$dtId` des jumeaux).

Les sections suivantes fournissent des exemples.

### <a name="basic-relationship-query"></a>Requête de relation de base

Voici un exemple de requête basée sur les relations. Cet extrait de code sélectionne tous les jumeaux numériques dont la propriété *ID* a la valeur « ABC », ainsi que tous les jumeaux numériques associés à ces jumeaux via une relation de *contenance*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship1":::

Le type de relation (`contains` dans l’exemple ci-dessus) est indiqué par le champ **name** de la relation dans sa [définition DTDL](concepts-models.md#basic-relationship-example).

> [!NOTE]
> Le développeur n’a pas besoin de mettre en corrélation ce `JOIN` avec une valeur de clé dans la clause `WHERE` (ni de spécifier une valeur de clé inline avec la définition `JOIN`). Cette corrélation est calculée automatiquement par le système, puisque les propriétés de relation identifient l’entité cible.

### <a name="query-by-the-source-or-target-of-a-relationship"></a>Interroger selon la source ou la cible d’une relation

Vous pouvez utiliser la structure de requête de relation pour identifier un jumeau numérique qui est la source ou la cible d’une relation.

Par exemple, vous pouvez commencer avec un jumeau source et suivre ses relations pour trouver les jumeaux cibles des relations. Voici un exemple de requête qui recherche les jumeaux cibles des relations de *flux* provenant du jumeau source du jumeau.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipSource":::

Vous pouvez également commencer avec la cible de la relation et effectuer le suivi de la relation pour retrouver le jumeau source. Voici un exemple de requête qui permet de trouver le jumeau source d’une relation de *flux* avec le jumeau cible du jumeau.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>Interroger les propriétés d’une relation

De même qu’il est possible de décrire les propriétés des jumeaux numériques via DTDL, les relations peuvent elles aussi avoir des propriétés. Vous pouvez interroger les jumeaux **selon les propriétés de leurs relations**.
Le langage d’Azure Digital Twins permet le filtrage et la projection des relations, en affectant un alias à la relation dans la clause `JOIN`.

Prenons l’exemple d’une relation *servicedBy* qui comprendrait une propriété *reportedCondition*. Dans la requête ci-dessous, cette relation se voit attribuer l’alias « R » afin de référencer sa propriété.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship2":::

Dans l’exemple ci-dessus, notez que *reportedCondition* est une propriété de la relation *servicedBy* (et non d’un jumeau numérique ayant une relation *servicedBy*).

### <a name="query-with-multiple-joins"></a>Interroger avec plusieurs clauses JOIN

Jusqu’à cinq clauses `JOIN` sont prises en charge dans une même requête, ce qui vous permet de traverser plusieurs niveaux de relations à la fois. 

Pour effectuer une requête à plusieurs niveaux de la relation, utilisez une instruction `FROM` suivie de N instructions `JOIN`, où les instructions `JOIN` expriment les relations basées sur le résultat d’une instruction `FROM` ou `JOIN` précédente.

Voici un exemple de requête à plusieurs JOIN, qui permet d’obtenir toutes les ampoules contenues dans les panneaux lumineux des pièces 1 et 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Compter les éléments

Vous pouvez compter le nombre d’éléments dans un jeu de résultats à l’aide de la clause `Select COUNT` :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount1":::

Ajoutez une clause `WHERE` pour compter le nombre d’éléments qui répondent à un certain critère. Voici quelques exemples de comptage avec un filtre appliqué en fonction du type de modèle de jumeau (pour plus d’informations sur cette syntaxe, consultez [Requête par modèle](#query-by-model) ci-dessous) :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount2":::

Vous pouvez également utiliser `COUNT` avec la clause `JOIN` . Voici une requête qui compte toutes les ampoules contenues dans les panneaux lumineux des salles 1 et 2 :

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Filtrer les résultats : sélectionner les meilleurs éléments

Vous pouvez sélectionner les meilleurs éléments dans une requête à l’aide de la clause `Select TOP`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Filtrer les résultats : spécifier un jeu de retour avec des projections

En utilisant des projections dans l’instruction `SELECT`, vous pouvez choisir les colonnes qui sont renvoyées par une requête. La projection est désormais prise en charge pour les propriétés primitives et complexes. Pour plus d’informations sur les projections avec Azure Digital Twins, consultez la [documentation de référence sur la clause SELECT](reference-query-clause-select.md#select-columns-with-projections).

Voici un exemple de requête qui utilise la projection pour retourner les jumeaux et les relations. La requête ci-dessous projette Consumer, Factory et Edge dans un scénario où une Factory avec l’ID *ABC* est liée au Consumer via une relation *Factory.customer*, et cette relation est présentée en tant que *Edge*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections1":::

Vous pouvez également utiliser la projection pour retourner une propriété d’un jumeau. La requête suivante projette la propriété *Name* des Consumers associés à la Factory avec l’ID *ABC* via une relation de *Factory.customer*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections2":::

Vous pouvez également utiliser la projection pour retourner une propriété d’une relation. Comme dans l’exemple précédent, la requête suivante projette la propriété *Name* des Consumers associés à la Factory avec un ID *ABC* via une relation de *Factory.customer*. Elle retourne maintenant également deux propriétés de cette relation : *Prop1* et *Prop2*. Pour ce faire, elle nomme la relation *Edge* et collecte ses propriétés.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections3":::

Vous pouvez également utiliser des alias pour simplifier les requêtes avec projection.

La requête suivante effectue les mêmes opérations que l’exemple précédent, mais elle associe les noms de propriété à `consumerName`, `first`, `second` et `factoryArea`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections4":::

Voici une requête similaire qui interroge le même jeu que ci-dessus, mais projette uniquement la propriété *Consumer.name* en tant que `consumerName` et projette la fabrique complète en tant que jumeau.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Créer des requêtes efficaces avec l’opérateur IN

Vous pouvez réduire considérablement le nombre de requêtes dont vous avez besoin en générant un tableau de jumeaux et en l’interrogeant à l’aide de l’opérateur `IN`. 

Par exemple, imaginons un scénario dans lequel des immeubles comprennent des étages, et ces étages comprennent des pièces. Pour rechercher des salles chauffées dans un immeuble, vous pouvez suivre ces étapes.

1. Recherchez les étages de l’immeuble en fonction de la relation `contains`.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWithout":::

2. Pour trouver des pièces, au lieu de prendre les étages un par un et d’exécuter une requête `JOIN` afin de rechercher les pièces de chacun d’eux, vous pouvez interroger une collection d’étages de l’immeuble (nommée Floor dans la requête ci-dessous).

    Dans l’application cliente :
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    Dans la requête :
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Autres exemples de requêtes composées

Vous pouvez **combiner** un des types de requête ci-dessus avec des opérateurs de combinaison pour inclure plus de détails dans une seule requête. Voici quelques autres exemples de requêtes composées qui interrogent plusieurs types de descripteur de jumeau à la fois.

* Parmi les appareils qui équipent Room 123 (la salle 123), retournez les appareils MxChip qui remplissent le rôle d’Operator (opérateur)
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples1":::
* Récupérez les jumeaux qui présentent une relation nommée *Contains* (Contient) avec un autre jumeau dont l’ID est *id1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples2":::
* Récupérez toutes les salles de ce modèle de salle qui sont contenues dans floor11 (l’étage11)
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Exécuter des requêtes avec l’API

Une fois que vous avez choisi une chaîne de requête, exécutez-la en appelant l’[API de requête](/rest/api/digital-twins/dataplane/query).

Vous pouvez appeler l’API directement ou utiliser l’un des [SDK](concepts-apis-sdks.md#overview-data-plane-apis) disponibles pour Azure Digital Twins.

L’extrait de code suivant illustre l’appel au [SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) à partir de l’application cliente :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

La requête utilisée dans cet appel retourne une liste de jumeaux numériques, que l’exemple ci-dessus représente avec des objets [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true). Le type de retour de vos données pour chaque requête dépend des termes que vous spécifiez avec l’ instruction `SELECT` :
* Les requêtes qui commencent par `SELECT * FROM ...` retournent une liste de jumeaux numériques (qui peuvent être sérialisés sous la forme d’objets `BasicDigitalTwin` ou d’autres types de jumeaux numériques personnalisés que vous avez peut-être créés).
* Les requêtes qui commencent au format `SELECT <A>, <B>, <C> FROM ...` retournent un dictionnaire avec des clés `<A>`, `<B>` et `<C>`.
* D’autres formats d’instructions `SELECT` peuvent être élaborés pour retourner des données personnalisées. Vous pouvez envisager de créer vos propres classes pour gérer des jeux de résultats personnalisés. 

### <a name="query-with-paging"></a>Requête avec pagination

Les appels de requête prennent en charge la pagination. Voici un exemple complet qui utilise `BasicDigitalTwin` en tant que type de résultat de requête avec gestion des erreurs et pagination :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [API et SDK Azure Digital Twins](concepts-apis-sdks.md), y compris l’API de requête qui est utilisée pour exécuter les requêtes de cet article.
