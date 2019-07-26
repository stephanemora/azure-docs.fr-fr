---
title: Indexation dans Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 633d0f619132ee93951cfe0dc329a7514a38ef57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240733"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Vue d’ensemble de l’indexation dans Azure Cosmos DB

Azure Cosmos DB est une base de données indépendante des schémas qui vous permet d’itérer sur votre application sans avoir à vous soucier de la gestion des schémas ou des index. Par défaut, Azure Cosmos DB indexe automatiquement toutes les propriétés de tous les éléments de votre [conteneur](databases-containers-items.md#azure-cosmos-containers) sans avoir à définir de schéma ou configurer d’index secondaires.

L’objectif de cet article est d’expliquer comment Azure Cosmos DB indexe les données et comment il utilise les index pour améliorer les performances des requêtes. Il est recommandé de parcourir cette section avant de découvrir comment personnaliser les [stratégies d’indexation](index-policy.md).

## <a name="from-items-to-trees"></a>Des éléments aux arborescences

Chaque fois qu’un élément est stocké dans un conteneur, son contenu est projeté sous forme de document JSON, puis converti en une représentation arborescente. Cela signifie que chaque propriété de cet élément est représentée en tant que nœud dans une arborescence. Un pseudo nœud est créé en tant que parent pour toutes les propriétés de premier niveau de l’élément. Les nœuds terminaux contiennent des valeurs scalaires réelles effectuées par un élément.

Par exemple, tenez compte de cet élément :

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

Notez comment les tableaux sont encodés dans l’arborescence : chaque entrée d’un tableau reçoit un nœud intermédiaire étiqueté avec l’index de cette entrée dans le tableau (0, 1, et ainsi de suite).

## <a name="from-trees-to-property-paths"></a>Des arborescences aux chemins de propriété

Azure Cosmos DB transforme les éléments en arborescence car il permet de référencer les propriétés par leurs chemins d’accès dans ces arborescences. Pour obtenir le chemin d’accès d’une propriété, nous pouvons parcourir l’arborescence entre le nœud racine et cette propriété, et concaténer les étiquettes de chaque nœud traversé.

Voici les chemins d’accès de chaque propriété de l’élément exemple décrit ci-dessus :

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Lorsqu’un élément est écrit, Azure Cosmos DB indexe efficacement le chemin d’accès de chaque propriété et sa valeur correspondante.

## <a name="index-kinds"></a>Types d’index

Azure Cosmos DB prend en charge deux types d’index :

Le type d’index **plage** est utilisé pour les types de requête suivants :

- Requêtes d’égalité : 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Requêtes de plage : 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (fonctionne pour `>`, `<`, `>=`, `<=`, `!=`)

- Requêtes `ORDER BY` :

   ```sql SELECT * FROM container c ORDER BY c.property```

- Requêtes `JOIN` : 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Les index plage sont utilisables sur des valeurs scalaires (chaîne ou nombre).

Le type d’index **spatial** est utilisé pour les types de requête suivants :

- Requêtes de distance géospatiale : 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- Géospatial dans les requêtes : 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Les index spatiaux sont utilisables sur des objets [GeoJSON](geospatial.md) correctement formatés . Les points, les LineStrings et les polygones sont actuellement pris en charge.

Le type d’index **plage** est utilisé pour les types de requête suivants :

- Requêtes `ORDER BY` sur plusieurs propriétés : 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Interrogation avec des index

Les chemins d’accès extraits lors de l’indexation des données facilitent la recherche de l’index lors du traitement d’une requête. En faisant correspondre la clause `WHERE` d’une requête avec la liste des chemins d’accès indexés, il est possible d’identifier très rapidement les éléments qui correspondent au prédicat de la requête.

Considérez la requête suivante : `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Le prédicat de requête (filtrage sur les éléments, où n’importe quelle localisation possède « France » comme pays) correspondrait au chemin surligné en rouge ci-dessous :

![Mise en correspondance d’un chemin d’accès spécifique au sein d’une arborescence](./media/index-overview/matching-path.png)

> [!NOTE]
> Une clause `ORDER BY` qui commande par une seule propriété a *toujours* besoin d’un index plage et échouera si le chemin d’accès qu’elle référence n’en a pas. De même, une requête multi `ORDER BY` nécessite *toujours* un index composite.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail l’indexation dans les articles suivants :

- [Stratégie d’indexation](index-policy.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
