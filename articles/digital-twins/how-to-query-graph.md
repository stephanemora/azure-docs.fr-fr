---
title: Interroger le graphe de jumeaux
titleSuffix: Azure Digital Twins
description: Découvrez comment interroger le graphe de jumeaux Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 93043874db6076b26d0fefe447db7acd83547442
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725582"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Interroger le graphe de jumeaux Azure Digital Twins

Cet article aborde en détail l’utilisation du [langage du magasin de requêtes Azure Digital Twins](concepts-query-language.md) dans le but d’interroger les données du [graphe de jumeaux](concepts-twins-graph.md). Vous exécutez des requêtes sur le graphe à l’aide des [**API de requête**](how-to-use-apis-sdks.md) Azure Digital Twins.

## <a name="query-syntax"></a>Syntaxe de requête

Voici quelques exemples de requêtes qui illustrent la structure du langage de requête et exécutent les opérations de requête possibles.

Obtenir les [jumeaux numériques](concepts-twins-graph.md) d’après leurs propriétés (y compris l’ID et les métadonnées) :
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

Obtenir les jumeaux numériques d’après le [modèle](concepts-models.md)
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> L’ID d’un jumeau numérique s’interroge à l’aide du champ de métadonnées `$dtId`.

## <a name="run-queries-with-an-api-call"></a>Exécuter des requêtes avec un appel d’API

Une fois que vous avez choisi une chaîne de requête, exécutez-la en appelant l’**API de requête**.
L’extrait de code suivant illustre cet appel à partir de l’application cliente :

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Cet appel retourne les résultats de la requête sous la forme d’un objet QueryResult. 

Les appels de requête prennent en charge la pagination. Voici un exemple complet de pagination et de gestion des erreurs :

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-based-on-relationships"></a>Requête basée sur des relations

Lorsque vous effectuez une interrogation basée sur les relations des jumeaux numériques, sachez que le langage du magasin de requêtes Azure Digital Twins a une syntaxe spéciale.

Les relations sont tirées (pull) puis ajoutées à l’étendue de la requête dans la clause `FROM`. Ce qui le distingue principalement des langages de type SQL « classiques » est que chaque expression de la clause `FROM` n’est pas une table. En effet, la clause `FROM` exprime une traversée de relation entre les entités, et elle est écrite avec une version Azure Digital Twins de `JOIN`. 

Rappelez-vous qu’avec les fonctionnalités de [modèle](concepts-models.md) d’Azure Digital Twins, les relations n’existent pas indépendamment des jumeaux. Cela signifie que le `JOIN` du langage du magasin de requêtes Azure Digital Twins est légèrement différent du `JOIN` SQL général, puisque les relations ne peuvent pas être interrogées de manière indépendante et doivent être liées à un jumeau.
Pour incorporer cette différence, le mot clé `RELATED` est utilisé dans la clause `JOIN` afin de référencer l’ensemble de relations d’un jumeau. 

La section suivante fournit plusieurs exemples.

> [!TIP]
> D’un point de vue conceptuel, cette fonctionnalité imite les fonctionnalités orientées document de CosmosDB, où `JOIN` peut être effectué sur les objets enfants d’un document. CosmosDB utilise le mot clé `IN` pour indiquer que `JOIN` est destiné à itérer au sein des éléments de tableau du document de contexte actuel.

### <a name="relationship-based-query-examples"></a>Exemples de requêtes basées sur les relations

Pour obtenir un jeu de données comprenant des relations, utilisez une instruction `FROM` suivie de N instructions `JOIN`, où les instructions `JOIN` expriment les relations basées sur le résultat d’une instruction `FROM` ou `JOIN` précédente.

Voici un exemple de requête basée sur les relations. Cet extrait de code sélectionne tous les jumeaux numériques dont la propriété *ID* a la valeur « ABC », ainsi que tous les jumeaux numériques associés à ces jumeaux via une relation de *contenance*. 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> Le développeur n’a pas besoin de mettre en corrélation ce `JOIN` avec une valeur de clé dans la clause `WHERE` (ni de spécifier une valeur de clé inline avec la définition `JOIN`). Cette corrélation est calculée automatiquement par le système, puisque les propriétés de relation identifient l’entité cible.

### <a name="query-the-properties-of-a-relationship"></a>Interroger les propriétés d’une relation

De même qu’il est possible de décrire les propriétés des jumeaux numériques via DTDL, les relations peuvent elles aussi avoir des propriétés. Le langage du magasin de requêtes Azure Digital Twins permet le filtrage et la projection des relations, en affectant un alias à la relation dans la clause `JOIN`. 

Prenons l’exemple d’une relation *servicedBy* qui comprendrait une propriété *reportedCondition*. Dans la requête ci-dessous, cette relation se voit attribuer l’alias « R » afin de référencer sa propriété.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

Dans l’exemple ci-dessus, notez que *reportedCondition* est une propriété de la relation *servicedBy* (et non d’un jumeau numérique ayant une relation *servicedBy*).

### <a name="query-limitations"></a>Limitations des requêtes

Il peut se passer jusqu’à 10 secondes avant que les modifications de votre instance ne soient reflétées dans les requêtes. Par exemple, si vous effectuez une opération telle que la création ou la suppression de jumeaux avec l’API DigitalTwins, le résultat peut ne pas apparaître immédiatement dans les requêtes de l’API de requête. Il suffit d’attendre quelques instants pour que le résultat apparaisse.

Dans la préversion, il existe d’autres limitations concernant l’utilisation de `JOIN`.
* Aucune sous-requête n’est prise en charge dans l’instruction `FROM`.
* La sémantique `OUTER JOIN` n’est pas prise en charge, ce qui signifie que si la relation a un rang égal à zéro, la « ligne » entière sera éliminée du jeu de résultats de sortie.
* Dans la préversion publique, la profondeur de la traversée du graphe est restreinte : seul un `JOIN` est autorisé par requête.
* La source des opérations `JOIN` est restreinte : la requête doit déclarer les jumeaux au début.

## <a name="query-best-practices"></a>Meilleures pratiques relatives aux requêtes

Voici quelques conseils concernant l’interrogation de données à l’aide d’Azure Digital Twins.

* Intéressez-vous au modèle de requête pendant la phase de conception du modèle. Vérifiez que les relations qui doivent être traitées dans une même requête sont modélisées sous la forme d’une relation à niveau unique.
* Concevez des propriétés de manière à éviter les grands jeux de résultats provenant de la traversée de graphe.
* Vous pouvez réduire considérablement le nombre de requêtes dont vous avez besoin en générant un tableau de jumeaux et en l’interrogeant à l’aide de l’opérateur `IN`. Par exemple, imaginons un scénario dans lequel des *immeubles* comprennent des *étages*, et ces *étages* comprennent des *pièces*. Pour rechercher les pièces d’un immeuble dont le niveau d’accès est chaud, vous pouvez :

    1. Rechercher les étages de l’immeuble en fonction de la relation `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Pour trouver des pièces, au lieu de prendre les étages un par un et d’exécuter une requête `JOIN` afin de rechercher les pièces de chacun d’eux, vous pouvez interroger une collection d’étages de l’immeuble (nommée *Floor* dans la requête ci-dessous).

        Dans l’application cliente :
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        Dans la requête :
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Les noms et les valeurs des propriétés respectent la casse, veillez donc à utiliser les noms exacts qui sont définis dans les modèles. Si les noms des propriétés sont mal orthographiés ou ne respectent pas la casse, le jeu de résultats sera vide et aucune erreur ne sera retournée.


## <a name="next-steps"></a>Étapes suivantes

Découvrez les [API et SDK Azure Digital Twins](how-to-use-apis-sdks.md), y compris l’API de requête qui est utilisée pour exécuter les requêtes de cet article.
