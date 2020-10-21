---
title: Mise à l'échelle du débit dans Azure Cosmos DB
description: Cet article explique comment Azure Cosmos DB met à l’échelle le débit dans les différentes régions où le compte Azure Cosmos est provisionné.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397349"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>Comment Azure Cosmos DB met-il à l'échelle le débit approvisionné à l'échelle mondiale ?

Dans Azure Cosmos DB, le débit approvisionné est représenté sous forme d’unités de requête par seconde (RU/s). Les unités de requête mesurent le coût des opérations en lecture et en écriture sur votre conteneur Cosmos comme le montre l’image suivante :

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Unités de requête" border="false":::

Vous pouvez approvisionner des unités de requête sur un conteneur Cosmos ou dans une base de données Cosmos. Les unités de requête approvisionnées sur un conteneur sont exclusivement disponibles pour les opérations effectuées sur ce conteneur. Les unités de requête approvisionnées sur une base de données sont partagées entre tous les conteneurs au sein de cette base de données (à l’exception des conteneurs avec des unités de requête attribuées exclusivement).

Pour mettre à l’échelle le débit approvisionné de manière élastique, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de service approvisionnées par seconde. Pour plus d’informations, consultez [Comment approvisionner le débit](set-throughput.md) et mettre à l’échelle de manière élastique des conteneurs et des bases de données Cosmos. Pour une mise à l’échelle globale du débit, vous pouvez à tout moment ajouter ou supprimer des régions de votre compte Cosmos. Pour plus d’informations, consultez [Ajouter/supprimer des régions à partir de votre compte de base de données](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Il est important d’associer plusieurs régions avec un compte Cosmos dans de nombreux scénarios pour obtenir une faible latence et une [haute disponibilité](high-availability.md) dans le monde entier.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Répartition du débit provisionné entre les régions

Si vous configurez le service d’unités de requête *« R »* sur un conteneur (ou dans une base de données) Cosmos, Cosmos DB garantit que les unités de requête *« R »* sont disponibles dans *chaque* région associée à votre compte Cosmos. Chaque fois que vous ajoutez une région à votre compte, Cosmos DB approvisionne automatiquement des unités de requête *« R »* dans la région qui vient d’être ajoutée. Les opérations effectuées sur votre conteneur Cosmos sont garanties pour obtenir des unités de requête *« R »* dans chaque région. Vous ne pouvez pas affecter sélectivement des unités de requête à une région spécifique. Les unités de requête approvisionnées pour un conteneur (ou une base de données) Cosmos sont approvisionnées pour toutes les régions associées à votre compte Cosmos.

En supposant qu’un conteneur Cosmos est configuré avec des unités de requête *« R »* et que *« N »* régions sont associées au compte Cosmos, alors :

- Si le compte Cosmos est configuré avec une seule région d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur = *R* x *N*.

- Si le compte Cosmos est configuré avec plusieurs régions d’écriture, le total des unités de requête disponibles dans le monde entier sur le conteneur= *R* x (*N*+1). Les unités de requête *R* supplémentaires sont approvisionnées automatiquement pour traiter les conflits de mise à jour de processus et le trafic anti-entropie dans les régions.

Votre choix de [modèle de cohérence](consistency-levels.md) affecte également le débit. Vous pouvez approximativement doubler le débit de lecture pour les niveaux de cohérence les plus souples (par exemple, *session*, *préfixe cohérent* et cohérence *éventuelle*) par rapport à des niveaux de cohérence plus stricts (par exemple, *obsolescence limitée* ou cohérence *forte*).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez apprendre à configurer le débit sur un conteneur ou une base de données :

* [Obtenir et définir le débit pour les conteneurs et les bases de données](set-throughput.md) 

