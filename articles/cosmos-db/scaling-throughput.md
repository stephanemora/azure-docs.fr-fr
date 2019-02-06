---
title: Mise à l'échelle du débit dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB met à l’échelle le débit de manière élastique
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: dd17b08a16dedf474b2a1eca8fa8034672610c1f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454434"
---
# <a name="globally-scale-provisioned-throughput"></a>Mettre à l’échelle le débit provisionné au niveau global 

Dans Azure Cosmos DB, le débit provisionné est représenté sous forme d’unités de requête par seconde (RU/s, au pluriel : RUs). Les unités de requête mesurent le coût des opérations en lecture et en écriture sur votre conteneur Cosmos comme le montre l’image suivante :

![Unités de requête](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Vous pouvez approvisionner des unités de requête sur un conteneur Cosmos ou dans une base de données Cosmos. Les unités de requête approvisionnées sur un conteneur sont exclusivement disponibles pour les opérations effectuées sur ce conteneur. Les unités de requête approvisionnées sur une base de données sont partagées entre tous les conteneurs au sein de cette base de données (à l’exception des conteneurs avec des unités de requête attribuées exclusivement).

Pour mettre à l’échelle le débit de manière élastique, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de service approvisionnées. Pour plus d’informations, consultez [Comment approvisionner le débit](set-throughput.md) et mettre à l’échelle de manière élastique des conteneurs et des bases de données Cosmos. Pour une mise à l’échelle globale du débit, vous pouvez à tout moment ajouter ou supprimer des régions de votre compte Cosmos. Pour plus d’informations, consultez [Ajouter/supprimer des régions à partir de votre compte de base de données](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Il est important d’associer plusieurs régions avec un compte Cosmos dans de nombreux scénarios pour obtenir une faible latence et une [haute disponibilité](high-availability.md) dans le monde entier.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Répartition du débit provisionné entre les régions

Si vous configurez le service d’unités de requête 'R' sur un conteneur (ou dans une base de données) Cosmos, Cosmos DB garantit que les unités de requête 'R' sont disponibles dans *chaque* région associée à votre compte Cosmos. Chaque fois que vous ajoutez une nouvelle région à votre compte, Cosmos DB approvisionne automatiquement des unités de requête 'R' dans la région qui vient d’être ajoutée. Les opérations effectuées sur votre conteneur Cosmos sont garanties pour obtenir des unités de requête 'R' dans chaque région. Vous ne pouvez pas affecter sélectivement des unités de requête à une région spécifique. Les unités de requête approvisionnées pour un conteneur (ou une base de données) Cosmos sont approvisionnées pour toutes les régions associées à votre compte Cosmos.

En supposant qu’un conteneur Cosmos est configuré avec des unités de requête 'R' et que 'N' régions sont associées au compte Cosmos, alors :

- Si le compte Cosmos est configuré avec une seule région d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur = R x N.

- Si le compte Cosmos est configuré avec plusieurs régions d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur = R x (N+1). Les unités de requête R supplémentaires sont approvisionnées automatiquement pour traiter les conflits de mise à jour de processus et le trafic anti-entropie dans les régions.

Votre choix de [modèle de cohérence](consistency-levels.md) affecte également le débit. Vous pouvez approximativement doubler le débit de lecture pendant la session, obtenir un préfixe cohérent et une cohérence éventuelle par rapport à l’obsolescence limitée ou à la cohérence forte.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite apprendre à configurer le débit à l’aide de l’article suivant :

* [Obtenir et définir le débit pour les conteneurs et les bases de données](set-throughput.md) 

