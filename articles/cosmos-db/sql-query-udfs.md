---
title: Fonctions définies par l’utilisateur dans Azure Cosmos DB
description: Découvrez les fonctions définies par l’utilisateur dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614334"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Fonctions définies par l’utilisateur dans Azure Cosmos DB

L’API SQL prend en charge les fonctions définies par l’utilisateur. Les fonctions scalaires définies par l’utilisateur vous permettent de passer zéro ou plusieurs arguments et de retourner un résultat contenant un seul argument. L’API vérifie que chaque argument est une valeur JSON légale.  

L’API étend la syntaxe SQL pour prendre en charge la logique d’application personnalisée à l’aide de fonctions définies par l’utilisateur. Vous pouvez inscrire les fonctions définies par l’utilisateur auprès de l’API SQL et les référencer dans les requêtes SQL. En fait, les fonctions définies par l’utilisateur sont conçues avec soin pour être appelées à partir de requêtes. En conséquence, les fonctions définies par l’utilisateur ne peuvent pas accéder à l’objet de contexte comme d’autres types JavaScript, tels que les procédures stockées et les déclencheurs. Les requêtes sont en lecture seule et peuvent s’exécuter sur des réplicas principaux ou secondaires. Les fonctions définies par l’utilisateur, contrairement à d’autres types JavaScript, sont conçues pour être exécutées sur des réplicas secondaires.

L’exemple suivant inscrit une fonction définie par l’utilisateur sous un conteneur d’éléments dans la base de données Cosmos. L’exemple crée une fonction définie par l’utilisateur dont le nom est `REGEX_MATCH`. Elle accepte les deux valeurs de chaîne JSON `input` et `pattern`, et vérifie si la première correspond au modèle spécifié dans la seconde à l’aide de la fonction `string.match()` de JavaScript.

## <a name="examples"></a>Exemples

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Utilisez maintenant cette fonction définie par l’utilisateur dans une projection de requête. Vous devez qualifier les fonctions définies par l’utilisateur avec le préfixe sensible à la casse `udf.` quand vous les appelez à partir de requêtes.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Les résultats sont :

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Vous pouvez utiliser la fonction définie par l’utilisateur qualifiée avec le préfixe `udf.` à l’intérieur d’un filtre, comme dans l’exemple suivant :

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Les résultats sont :

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Par essence, les fonctions définies par l’utilisateur sont des expressions scalaires valides que vous pouvez utiliser dans des projections et des filtres.

Pour développer la puissance des fonctions définies par l’utilisateur, étudiez un autre exemple de logique conditionnelle :

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

L’exemple suivant teste la fonction définie par l’utilisateur :

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Les résultats sont :

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Si les propriétés auxquelles font référence les paramètres des fonctions définies par l’utilisateur ne sont pas disponibles dans la valeur JSON, les paramètres sont considérés comme non définis et l’appel des fonctions définies par l’utilisateur est ignoré. De même, si le résultat des fonctions définies par l’utilisateur est indéfini, il n’est pas inclus dans le résultat.

Comme le montrent les exemples précédents, les fonctions définies par l’utilisateur intègrent la puissance du langage JavaScript à l’API SQL. Les fonctions définies par l’utilisateur fournissent une interface programmable enrichie qui permet de concevoir une logique conditionnelle procédurale complexe à l’aide de fonctionnalités de runtime JavaScript intégrées. L’API SQL fournit les arguments aux fonctions définies par l’utilisateur pour chaque élément source à l’étape actuelle du traitement concernant la clause WHERE ou SELECT. Le résultat est intégré au pipeline d’exécution générale de manière fluide. En résumé, les fonctions définies par l’utilisateur sont des outils efficaces pour mener à bien des logiques métier complexes dans le cadre de requêtes.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions système](sql-query-system-functions.md)
- [Agrégats](sql-query-aggregates.md)