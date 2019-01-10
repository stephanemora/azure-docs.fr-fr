---
title: Clés de partition synthétiques dans Azure Cosmos DB
description: Apprenez à utiliser des clés de partition synthétiques dans vos conteneurs Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 37d220a13aec99de94afa3357db1462d11f8662c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043362"
---
# <a name="create-a-synthetic-partition-key"></a>Créer une clé de partition synthétique

Il est recommandé de disposer d'une clé de partition comportant de nombreuses valeurs distinctes (plusieurs centaines ou plusieurs milliers). L'objectif est de répartir vos données et votre charge de travail de manière uniforme entre les éléments associés à ces valeurs de clé de partition. En l'absence d'une telle propriété dans vos données, une clé de partition synthétique peut être construite. Les sections suivantes décrivent plusieurs techniques de base pour générer une clé de partition synthétique pour votre conteneur.

## <a name="concatenating-multiple-properties-of-an-item"></a>Concaténer plusieurs propriétés d’un élément

Vous pouvez former une clé de partition en concaténant plusieurs valeurs de propriété dans une propriété `partitionKey` artificielle. Ces clés sont appelées des clés synthétiques. Considérons l’exemple de document suivant :

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pour le document précédent, une des options consiste à définir /deviceId ou /date en tant que clé de partition, si vous souhaitez partitionner votre conteneur en fonction de l'ID du périphérique ou de la date. Une autre option consiste à concaténer ces deux valeurs dans une propriété `partitionKey` synthétique utilisée comme clé de partition.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Dans les scénarios en temps réel, une base de données peut contenir des milliers de documents. Par conséquent, au lieu d'ajouter la clé synthétique manuellement, vous devez définir une logique côté client pour concaténer des valeurs et insérer la clé synthétique dans les documents.

## <a name="using-a-partition-key-with-random-suffix"></a>Utiliser une clé de partition avec suffixe aléatoire

Pour répartir la charge de travail plus uniformément, une autre stratégie consiste à ajouter un nombre aléatoire à la fin de la valeur de la clé de partition. Cette répartition des éléments permet d'effectuer des opérations d'écriture parallèles sur des partitions.

Par exemple, si une clé de partition représente une date, vous pouvez choisir un nombre aléatoire compris entre 1 et 400 et le concaténer en tant que suffixe de la date. Cette méthode aboutit à des valeurs de clé de partition telles que 2018-08-09.1, 2018-08-09.2, etc., jusqu'à 2018-08-09.400. Comme la clé de partition est aléatoire, les opérations d'écriture effectuées quotidiennement sur le conteneur sont réparties de manière uniforme sur plusieurs partitions. Cette méthode optimise le parallélisme et le débit global.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Utiliser une clé de partition avec suffixes précalculés 

Bien que la stratégie aléatoire puisse considérablement améliorer le débit d'écriture, il est difficile de lire un élément spécifique car vous ne connaissez pas la valeur de suffixe utilisée lors de l'écriture de l'élément. Pour faciliter la lecture d'éléments individuels, vous pouvez utiliser la stratégie des suffixes précalculés. Au lieu d'utiliser un nombre aléatoire pour répartir les éléments entre les partitions, utilisez un nombre calculé en fonction de ce que vous souhaitez interroger.

Reprenons l'exemple précédent, dans lequel un conteneur utilise une date dans la clé de partition. Supposons maintenant que chaque élément possède un attribut NIV (Numéro d'Identification de Véhicule) accessible et que vous exécutez souvent des requêtes pour rechercher des éléments par NIV, en plus de la date. Avant que votre application n'inscrive l'élément dans le conteneur, elle peut calculer un suffixe basé sur le NIV et l'ajouter à la date de la clé de partition. Le calcul peut générer un nombre compris entre 1 et 400, uniformément réparti et semblable aux résultats obtenus avec la méthode de la stratégie aléatoire. La valeur de la clé de partition sera alors la date concaténée avec le résultat calculé.

Avec cette stratégie, les écritures sont uniformément réparties sur les valeurs de clé de partition et sur les partitions. Vous pouvez facilement lire un élément et une date particuliers, car vous pouvez calculer la valeur de la clé de partition pour un numéro d'identification de véhicule spécifique. L'avantage de cette méthode est qu'elle vous évite de créer une clé de partition à chaud unique (la clé de partition qui prend toute la charge de travail). 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le concept de partitionnement, consultez les articles suivants :

* En savoir plus sur les [partitions logiques](partition-data.md)
* En savoir plus sur le [provisionnement du débit sur des conteneurs et bases de données Cosmos](set-throughput.md)
* En savoir plus sur le [provisionnement du débit sur un conteneur Cosmos](how-to-provision-container-throughput.md)
* En savoir plus sur le [provisionnement du débit sur une base de données Cosmos](how-to-provision-database-throughput.md)
