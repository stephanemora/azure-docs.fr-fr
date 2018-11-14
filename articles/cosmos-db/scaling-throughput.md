---
title: Mise à l'échelle du débit dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB met à l’échelle le débit de manière élastique
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: d29f01c7f953ed211b429e41b844a01c67e41054
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282371"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Mise à l'échelle du débit dans Azure Cosmos DB

Dans Azure Cosmos DB, le débit approvisionné est représenté sous forme d’unités de requête unités par seconde (RU/s). Les unités de requête mesurent le coût des opérations en lecture et en écriture sur votre conteneur Cosmos comme le montre l’image suivante :

![Unités de requête](./media/scale-throughput/figure1.png)

Vous pouvez approvisionner des unités de requête sur un conteneur Cosmos ou dans une base de données Cosmos. Les unités de requête approvisionnées sur un conteneur sont exclusivement disponibles pour les opérations effectuées sur ce conteneur. Les unités de requête approvisionnées sur une base de données sont partagées entre tous les conteneurs au sein de cette base de données (à l’exception des conteneurs avec des unités de requête attribuées exclusivement).

Pour mettre à l’échelle le débit de manière élastique, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de service approvisionnées. Pour plus d’informations, consultez [Comment approvisionner le débit](set-throughput.md) et mettre à l’échelle de manière élastique des conteneurs et des bases de données Cosmos. Pour une mise à l’échelle globale du débit, vous pouvez à tout moment ajouter ou supprimer des régions de votre compte Cosmos. Pour plus d’informations, consultez [Comment ajouter ou supprimer des régions dans votre compte Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Il est important d’associer plusieurs régions avec un compte Cosmos dans de nombreux scénarios pour obtenir une faible latence et une [haute disponibilité](high-availability.md) dans le monde entier.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>façon dont le débit approvisionné est réparti entre les régions

Si vous configurez le service d’unités de requête 'R' sur un conteneur (ou dans une base de données) Cosmos, Cosmos DB garantit que les unités de requête 'R' sont disponibles dans *chaque* région associée à votre compte Cosmos. Chaque fois que vous ajoutez une nouvelle région à votre compte, Cosmos DB approvisionne automatiquement des unités de requête 'R' dans la région qui vient d’être ajoutée. Les opérations effectuées sur votre conteneur Cosmos sont garanties pour obtenir des unités de requête 'R' dans chaque région. Vous ne pouvez pas affecter sélectivement des unités de requête à une région spécifique. Les unités de requête approvisionnées pour un conteneur (ou une base de données) Cosmos sont approvisionnées pour toutes les régions associées à votre compte Cosmos.

En supposant qu’un conteneur Cosmos est configuré avec des unités de requête 'R' et que 'N' régions sont associées au compte Cosmos, alors :

- Si le compte Cosmos est configuré avec une seule région d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur = R x N.

- Si le compte Cosmos est configuré avec plusieurs régions d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur = R x (N+1). Les unités de requête R supplémentaires sont approvisionnées automatiquement pour traiter les conflits de mise à jour de processus et le trafic anti-entropie dans les régions.

Votre choix de [modèle de cohérence](consistency-levels.md) affecte également le débit. Vous pouvez approximativement doubler le débit de lecture pendant la session, obtenir un préfixe cohérent et une cohérence éventuelle par rapport à l’obsolescence limitée ou à la cohérence forte.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite apprendre à configurer le débit à l’aide de l’article suivant :

* [Obtenir et définir le débit pour les conteneurs et les bases de données](set-throughput.md) 

