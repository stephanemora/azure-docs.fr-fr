---
title: Créez une clé de partition synthétique dans Azure Cosmos DB pour répartir uniformément vos données et la charge de travail.
description: Apprenez à utiliser des clés de partition synthétiques dans vos conteneurs Azure Cosmos
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762733"
---
# <a name="create-a-synthetic-partition-key"></a>Créer une clé de partition synthétique

Il est recommandé d’avoir une clé de partition avec de nombreuses valeurs distinctes, telles que des centaines voire des milliers. L'objectif est de répartir vos données et votre charge de travail de manière uniforme entre les éléments associés à ces valeurs de clé de partition. Si une telle propriété n’existe pas dans vos données, vous pouvez construire un *clé de partition synthétique*. Ce document décrit plusieurs techniques de base pour générer une clé de partition synthétiques pour votre conteneur Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concaténer plusieurs propriétés d’un élément

Vous pouvez former une clé de partition en concaténant plusieurs valeurs de propriété dans une propriété `partitionKey` artificielle. Ces clés sont appelées des clés synthétiques. Considérons l’exemple de document suivant :

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pour le document précédent, une option consiste à définir /deviceId ou /date comme clé de partition. Utilisez cette option, si vous souhaitez partitionner votre conteneur en fonction des ID d’appareil ou date. Une autre option consiste à concaténer ces deux valeurs dans une propriété `partitionKey` synthétique utilisée comme clé de partition.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Dans les scénarios en temps réel, vous pouvez avoir des milliers d’articles dans une base de données. Au lieu d’ajouter manuellement la clé synthétique, définissent la logique côté client pour concaténer les valeurs et insérer la clé synthétique dans les éléments de vos conteneurs Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Utiliser une clé de partition avec suffixe aléatoire

Pour répartir la charge de travail plus uniformément, une autre stratégie consiste à ajouter un nombre aléatoire à la fin de la valeur de la clé de partition. Cette répartition des éléments vous permet d'effectuer des opérations d'écriture parallèles sur des partitions.

Par exemple, si une clé de partition représente une date. Vous pouvez choisir un nombre aléatoire compris entre 1 et 400 et concaténer en guise de suffixe pour la date. Cette méthode génère des valeurs de clé de partition comme `2018-08-09.1`,`2018-08-09.2`, et ainsi de suite, via `2018-08-09.400`. La clé de partition étant aléatoire, les opérations d'écriture effectuées quotidiennement sur le conteneur sont réparties de manière uniforme sur plusieurs partitions. Cette méthode optimise le parallélisme et le débit global.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Utiliser une clé de partition avec des suffixes précalculées 

La stratégie de suffixe aléatoire peut améliorer considérablement le débit d’écriture, mais il est difficile à lire un élément spécifique. Vous ne connaissez pas la valeur de suffixe utilisé lorsque vous avez écrit l’élément. Pour le rendre plus facile à lire des éléments individuels, utilisez la stratégie de suffixes précalculées. Au lieu d’utiliser un nombre aléatoire pour distribuer les éléments entre les partitions, utilisez un nombre est calculé en fonction de quelque chose que vous souhaitez interroger.

Prenons l’exemple précédent, où un conteneur utilise une date comme clé de partition. Supposons maintenant que chaque élément a un `Vehicle-Identification-Number` (`VIN`) attribut que nous souhaitons accéder. En outre, supposons que vous exécutez souvent des requêtes pour rechercher des éléments par le `VIN`, en plus de la date. Avant que votre application n'inscrive l'élément dans le conteneur, elle peut calculer un suffixe basé sur le NIV et l'ajouter à la date de la clé de partition. Le calcul peut générer un nombre compris entre 1 et 400 est réparti uniformément. Ce résultat est similaire aux résultats générés par la méthode de stratégie de suffixe aléatoire. La valeur de la clé de partition correspond alors à la date concaténée avec le résultat calculé.

Avec cette stratégie, les écritures sont uniformément réparties sur les valeurs de clé de partition et sur les partitions. Vous pouvez facilement lire un élément particulier et la date, étant donné que vous pouvez calculer la valeur de clé de partition pour un spécifique `Vehicle-Identification-Number`. L’avantage de cette méthode est que vous pouvez éviter la création d’une clé de partition à chaud unique, par exemple, une clé de partition qui prend la charge de travail. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le concept de partitionnement, consultez les articles suivants :

* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [approvisionner le débit sur des conteneurs et bases de données Azure Cosmos](set-throughput.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
