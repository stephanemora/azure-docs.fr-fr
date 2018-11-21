---
title: Unités de requête et débit dans Azure Cosmos DB
description: Découvrez les besoins en unités de requête dans Azure Cosmos DB, et comment les spécifier et les estimer.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 709cd16c7eee30dd2d88ea87b5f704ad20530ffb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621341"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB

Avec Azure Cosmos DB, vous payez pour le débit que vous provisionnez et le stockage que vous utilisez sur une base horaire. Le débit doit être provisionné pour veiller à ce que suffisamment de ressources système soient en permanence disponibles pour votre base de données et ce, afin de satisfaire ou de dépasser le contrat SLA Cosmos DB.

Cosmos DB prend en charge différentes API (SQL, MongoDB, Cassandra, Gremlin et Table). Chaque API possède son propre ensemble d’opérations de base de données, qui vont des simples opérations de lecture et d'écriture de points aux requêtes complexes. Chaque opération de base de données consomme des ressources système en fonction de sa complexité.  Le coût de toutes les opérations de base de données est normalisé par Cosmos DB et exprimé en termes d’unités de requête (RU). Le coût de lecture d'un élément de 1 Ko est d'une unité de requête (1 RU). Toutes les autres opérations de base de données se voient aussi attribuer un coût en termes d'unités de requête. Quelle que soit l’API que vous utilisiez pour interagir avec votre conteneur Cosmos et l’opération de base de données (écriture, lecture, requête), les coûts sont toujours exprimés en termes d'unités de requête.

Vous pouvez considérer les RU/s en tant que devise pour le débit. Les RU/s correspondent à une devise basée sur la vitesse, indépendamment des ressources système requises (UC, IOPS et mémoire, notamment) pour effectuer les opérations de base de données prises en charge par Cosmos DB. L’illustration suivante montre les unités de requête consommées par les différentes opérations de base de données :

![Les opérations de base de données consomment des unités de requête](./media/request-units/request-units.png)

Pour gérer et planifier la capacité, Cosmos DB veille à ce que le nombre d’unités de requête d'une opération de base de données spécifique sur un jeu de données spécifique soit déterministe. Vous pouvez suivre le nombre d’unités de requête consommées par une opération de base de données en examinant l’en-tête de réponse. Après avoir compris les facteurs qui ont un impact sur les frais d’unités de requête et cerné les demandes de débit de votre application, vous pouvez exécuter votre application de la manière la plus rentable possible.

Facturé sur une base horaire, vous configurez le nombre d’unités de requête de votre application par seconde et par incréments de 100 RU/s. Afin de faire évoluer le débit provisionné pour votre application, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de requête (par incréments ou décréments de 100 RU/s) par programmation ou à l'aide du portail Microsoft Azure.

Vous pouvez configurer le débit selon deux niveaux de granularité distincts : 

* **Conteneurs**. Pour plus d'informations, consultez [Provisionnement du débit sur un conteneur Cosmos](how-to-provision-container-throughput.md).
* **Bases de données**. Pour plus d'informations, consultez [Provisionnement du débit sur un base de données Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête

Lorsque vous évaluez le nombre de RU/s à provisionner, il est important à prendre en compte les facteurs suivants :

* **Taille de l’élément** : la taille d’un élément augmentant, le nombre d'unités de requête consommées pour lire ou écrire l'élément augmente aussi.

* **Indexation des documents** : par défaut, chaque élément est automatiquement indexé. Moins d’unités de requête sont consommées si vous choisissez de ne pas indexer certains de vos éléments dans un conteneur.

* **Nombre de propriétés de l'élément** : sur la base d'une indexation par défaut de toutes les propriétés, le nombre d'unités de requête consommées pour écrire un élément augmente en même temps que le nombre de propriétés de l'élément.

* **Propriétés indexées** : une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d’unités de requête pour les opérations d’écriture en limitant le nombre de propriétés indexées.

* **Cohérence des données** : les niveaux de cohérence de type fort et obsolescence limitée consomment approximativement deux fois plus d'unités de requête lors des opérations de lecture que les niveaux de cohérence de type flexible.

* **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête consommées pour une opération. Le nombre de résultats de requêtes, le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur, la taille des données sources, la taille du jeu de résultats et les projections ont une influence sur le coût des opérations de requête. Cosmos DB veille à ce que la même requête sur les mêmes données coûte toujours le même nombre d’unités de requête lors des exécutions qui se répètent.

* **Utilisation des scripts** : comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d'unités de requête en fonction de la complexité des opérations effectuées. Lors du développement de votre application, inspectez l’en-tête des frais de requêtes pour mieux comprendre la capacité des unités de requête consommée par opération.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [provisionnement du débit pour les conteneurs et bases de données Cosmos DB](set-throughput.md)
* En savoir plus sur les [partitions logiques](partition-data.md)
* En savoir plus sur la [mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* En savoir plus sur le [provisionnement du débit sur un conteneur Cosmos](how-to-provision-container-throughput.md)
* En savoir plus sur le [provisionnement du débit sur une base de données Cosmos](how-to-provision-database-throughput.md)
