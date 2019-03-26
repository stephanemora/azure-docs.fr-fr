---
title: Mise à l'échelle du débit dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB met à l’échelle le débit de manière élastique
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407434"
---
# <a name="globally-scale-provisioned-throughput"></a>Mettre à l’échelle le débit provisionné au niveau global 

Dans Azure Cosmos DB, débit approvisionné est représenté en tant que requête unités par seconde (RU/s ou la forme plurielle RUs). Les unités de requête mesurent le coût des opérations en lecture et en écriture sur votre conteneur Cosmos comme le montre l’image suivante :

![Unités de requête](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Vous pouvez approvisionner des unités de requête sur un conteneur Cosmos ou dans une base de données Cosmos. Unités de requête approvisionnées sur un conteneur sont exclusivement disponibles pour les opérations effectuées sur ce conteneur. Les unités de requête approvisionnées sur une base de données sont partagées entre tous les conteneurs au sein de cette base de données (à l’exception des conteneurs avec des unités de requête attribuées exclusivement).

En toute flexibilité mise à l’échelle de débit approvisionné, vous pouvez augmenter ou diminuer les RU/s approvisionné à tout moment. Pour plus d’informations, consultez [procédures relatives à approvisionner le débit](set-throughput.md) et mettre à l’échelle des bases de données et les conteneurs Cosmos. Dans le monde entier mise à l’échelle de débit, vous pouvez ajouter ou supprimer des régions de votre compte Cosmos à tout moment. Pour plus d’informations, consultez [Ajouter/supprimer des régions à partir de votre compte de base de données](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Association de plusieurs régions avec un compte Cosmos est important dans de nombreux scénarios - pour obtenir une latence faible et [haute disponibilité](high-availability.md) dans le monde entier.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Répartition du débit provisionné entre les régions

Si vous configurez *'R'* RUs sur un conteneur Cosmos (ou de base de données), Cosmos DB garantit que *'R'* RU sont disponibles dans *chaque* région associée à votre compte Cosmos. Chaque fois que vous ajoutez une nouvelle région à votre compte, Cosmos DB approvisionne automatiquement *'R'* RUs dans la région qui vient d’être ajoutée. Les opérations effectuées par rapport à votre conteneur Cosmos sont sûrs d’obtenir *'R'* RUs dans chaque région. Vous ne pouvez pas affecter sélectivement des unités de requête à une région spécifique. Les unités de requête approvisionnées sur un conteneur de Cosmos (ou de la base de données) sont provisionnées dans toutes les régions associées à votre compte Cosmos.

En supposant qu’un conteneur Cosmos est configuré avec *'R'* unités de requête et il n’y *« n »* régions associées au compte Cosmos, puis :

- Si le compte Cosmos est configuré avec une région d’écriture unique, les total d’unités de requête disponibles dans le monde entier sur le conteneur = *R* x *N*.

- Si le compte Cosmos est configuré avec plusieurs régions d’écriture, les total d’unités de requête disponibles dans le monde entier sur le conteneur = *R* x (*N*+ 1). Supplémentaires *R* unités de requête sont automatiquement configurées pour les conflits de mise à jour de processus et d’entropie de lutte contre le trafic entre les régions.

Votre choix de [modèle de cohérence](consistency-levels.md) affecte également le débit. Vous pouvez obtenir environ 2 x débit de lecture pour les niveaux de cohérence plus souples (par exemple, *session*, *préfixe cohérent* et *éventuelle* cohérence) par rapport à les niveaux de cohérence plus forts (par exemple, *obsolescence* ou *fort* cohérence).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez apprendre à configurer le débit sur un conteneur ou d’une base de données :

* [Obtenir et définir le débit pour les conteneurs et les bases de données](set-throughput.md) 

