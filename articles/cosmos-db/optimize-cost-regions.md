---
title: Optimiser les coûts des déploiements multirégions dans Azure Cosmos DB
description: Cet article explique comment gérer les coûts des déploiements multirégions dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8d98c9a7e58f08d9ad63183805cd6cd0d2ab3b3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570196"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimiser le coût multirégion dans Azure Cosmos DB

Vous pouvez ajouter et supprimer des régions dans votre compte Azure Cosmos à tout moment. Le débit que vous configurez pour différentes bases de données et conteneurs Azure Cosmos est réservé dans chaque région associée à votre compte. Si le débit approvisionné par heure, qui est la somme des RU/s configurées dans l’ensemble des bases de données et des conteneurs de votre compte Azure Cosmos, est égal à `T` et si le nombre de régions Azure associées à votre compte de base de données est de `N`, alors le débit total approvisionné pour votre compte Cosmos pour une heure donnée est égal à :

1. `T x N RU/s` si votre compte Azure Cosmos est configuré avec une seule région d’écriture. 

1. `T x (N+1) RU/s` si votre compte Azure Cosmos est configuré de telle sorte que toutes les régions peuvent traiter les écritures. 

Le débit approvisionné avec une seule région d’écriture coûte 0,008 $/heure pour 100 RU/seconde et le débit approvisionné avec plusieurs régions accessibles en écriture coûte 0,016 $/heure pour 100 RU/seconde. Pour en savoir plus, voir la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/) d’Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Coûts pour plusieurs régions d’écriture

Dans un système d’écritures multirégions, les unités de requête nettes disponibles pour les opérations d’écriture sont multipliées par `N`, où `N` correspond au nombre de régions d’écriture. Contrairement aux régions d’écriture uniques, toutes les régions sont désormais accessibles en écriture et doivent prendre en charge la résolution des conflits. La quantité de charges de travail en écriture a augmenté. Du point de vue du coût de planification, pour effectuer `M` RU/seconde d’écritures dans le monde, vous devez approvisionner M `RUs` au niveau de la base de données ou du conteneur. Vous pouvez ensuite ajouter autant de régions que vous le souhaitez et les utiliser pour les écritures pour effectuer `M` RU d’écritures dans le monde entier. 

### <a name="example"></a>Exemple

Supposons que vous disposez d’un conteneur dans la région USA Ouest qui est provisionné avec un débit de 10 000 unités de requêtes par seconde et qui stocke 1 To de données ce mois. Prenons un exemple. Vous ajoutez trois régions : USA Est, Europe Nord et Asie Est. Les capacités de stockage et les débits de ces régions sont identiques. Vous souhaitez pouvoir écrire dans les conteneurs des 4 régions à partir de votre application globalement distribuée. Votre facture mensuelle totale est (en supposant un mois de 31 jours) :

|**Item**|**Utilisation (mensuelle)**|**Tarif**|**Coût mensuel**|
|----|----|----|----|
|Facture de débit pour le conteneur de la région USA Ouest (plusieurs régions d’écriture) |10 000 RU/seconde x 24 x 31 |0,016 $ pour 100 RU/s par heure |$1 190,40 |
|Facture de débit pour 3 régions supplémentaires : USA Est, Europe Nord et Asie Est (plusieurs régions d’écriture) |(3 + 1) x 10 000 RU/s x 24 x 31 |0,016 $ pour 100 RU/s par heure |$4 761,60 |
|Facture de stockage pour le conteneur de la région USA Ouest |1 To (ou 1 024 Go) |0,25 $/Go |256 $ |
|Facture de stockage pour 3 régions supplémentaires (USA Est, Europe Nord et Asie Est) |3 * 1 Go (ou 3 072 Go) |0,25 $/Go |768 $ |
|**Total**|||**6 976 $** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Améliorer l’utilisation du débit par région

Si vous avez une utilisation inefficace, par exemple, une ou plusieurs régions sous-utilisées ou trop utilisées, vous pouvez procéder comme suit pour améliorer l’utilisation du débit :  

1. Commencez par optimiser le débit provisionné (RU) dans la région d’écriture, puis utilisez les RU au maximum dans les régions de lecture en utilisant le flux de modification de la région de lecture. 

2. Les lectures et écritures de plusieurs régions peuvent être mises à l’échelle sur toutes les régions associées au compte Azure Cosmos. 

3. Surveillez l’activité dans vos régions, et ajoutez/supprimez des régions à la demande pour la mise à l’échelle de votre débit de lectures/écritures.

## <a name="next-steps"></a>Étapes suivantes

Pour continuer à développer vos connaissances sur l’optimisation des coûts dans Azure Cosmos DB, consultez les articles suivants :

* En savoir plus sur l’[optimisation pour le développement et le test](optimize-dev-test.md)
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des lectures et écritures](optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des requêtes](optimize-cost-queries.md)

