---
title: Indexation dans Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 44706e5ebe2442dcb45dfc45e2c322938cf7dca9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068654"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>L’indexation dans Azure Cosmos DB - vue d’ensemble

Azure Cosmos DB est une base de données indépendante du schéma qui vous permet d’effectuer une itération sur votre application sans avoir à gérer avec la gestion des schémas ou index. Par défaut, Azure Cosmos DB indexe automatiquement chaque propriété pour tous les éléments dans votre [conteneur](databases-containers-items.md#azure-cosmos-containers) sans avoir à définir n’importe quel schéma ou configurer des index secondaires.

L’objectif de cet article est d’expliquer comment Azure Cosmos DB indexe les données et comment il utilise des index pour améliorer les performances de requête. Il est recommandé de suivre cette section avant d’Explorer la personnalisation [stratégies d’indexation](index-policy.md).

## <a name="from-items-to-trees"></a>À partir d’éléments aux arborescences

Chaque fois qu’un élément est stocké dans un conteneur, son contenu est projeté comme un document JSON, puis converti en une représentation sous forme d’arborescence. Cela signifie que chaque propriété de cet élément est représentée en tant que nœud dans une arborescence. Un nœud racine de pseudo est créé en tant que parent pour toutes les propriétés de premier niveau de l’élément. Les nœuds feuille contiennent des valeurs scalaires réelles effectuées par un élément.

Par exemple, prenez en compte cet élément :

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

Il serait représenté par l’arborescence suivante :

![L’élément précédent représenté sous forme d’arborescence](./media/index-overview/item-as-tree.png)

Notez la manière dont les tableaux sont encodés dans l’arborescence : chaque entrée dans un tableau Obtient un nœud intermédiaire étiqueté avec l’index de cette entrée dans le tableau (0, 1 etc..).

## <a name="from-trees-to-property-paths"></a>À partir d’arborescences pour les chemins d’accès de propriété

Pourquoi Azure Cosmos DB transforme des éléments dans des arborescences parce, car elle permet les propriétés devant être référencés par leurs chemins d’accès au sein de ces arborescences. Pour obtenir le chemin d’accès pour une propriété, nous pouvons parcourir l’arborescence à partir du nœud racine à cette propriété et concaténer les étiquettes de chaque nœud traversé.

Voici les chemins d’accès pour chaque propriété de l’élément de l’exemple décrit ci-dessus :

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quand un élément est écrit, Azure Cosmos DB indexe efficacement le chemin d’accès de chaque propriété et sa valeur correspondante.

## <a name="index-kinds"></a>Types d’index

Azure Cosmos DB prend actuellement en charge deux types d’index :

Le **plage** type d’index est utilisé pour :

- requêtes d’égalité : 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Les requêtes de plage : 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (fonctionne pour `>`, `<`, `>=`, `<=`, `!=`)

- `ORDER BY` requêtes :

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` requêtes : 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Index de plage peuvent être utilisés sur des valeurs scalaires (chaîne ou nombre).

Le **spatial** type d’index est utilisé pour :

- requêtes géospatiales à distance : 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- Geospatial dans des requêtes : 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Les index spatiaux peuvent être utilisés sur correctement mis en forme [GeoJSON](geospatial.md) objets. Points, LineStrings et polygones sont actuellement pris en charge.

Le **composite** type d’index est utilisé pour :

- `ORDER BY` requêtes sur plusieurs propriétés : 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Interrogation avec des index

Les chemins d’accès extraites lors de l’indexation des données facilitent la recherche de l’index lors du traitement d’une requête. En mettant en correspondance le `WHERE` clause d’une requête avec la liste des chemins d’accès indexés, il est possible d’identifier les éléments qui correspondent au prédicat de requête de très rapidement.

Par exemple, considérez la requête suivante : `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Le prédicat de requête (filtrage sur les éléments, où n’importe quel emplacement a « France » en tant que son pays) renverrait le chemin d’accès mis en surbrillance en rouge ci-dessous :

![Mise en correspondance un chemin d’accès spécifique au sein d’une arborescence](./media/index-overview/matching-path.png)

> [!NOTE]
> Un `ORDER BY` clause trie par une seule propriété *toujours* a besoin d’une plage d’index et échoue si le chemin d’accès, elle fait référence à n’en possède pas. De même, un multiple `ORDER BY` requête *toujours* a besoin d’un index composite.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail l’indexation dans les articles suivants :

- [Stratégie d’indexation](index-policy.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
