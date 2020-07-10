---
title: Modèle de tarification d’Azure Cosmos DB
description: Cet article explique le modèle de tarification d’Azure Cosmos DB et comment il simplifie la planification de coûts.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 05a76a1434b5a63229b335fb3973897896ec733d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955810"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modèle de tarification dans Azure Cosmos DB 

Le modèle de tarification d’Azure Cosmos DB simplifie la gestion et la planification des coûts. Avec Azure Cosmos DB, vous payez pour le débit que vous approvisionnez et le stockage que vous utilisez.

* **Débit approvisionné** : le débit approvisionné (également appelé un débit réservé) garantit des performances élevées quelle que soit l’échelle. Vous spécifiez le débit (RU/s) dont vous avez besoin et Azure Cosmos DB consacre les ressources nécessaires pour garantir le débit configuré. Vous payez à l’heure le débit provisionné maximal pour une heure donnée.

   > [!NOTE]
   > Étant donné que le modèle de débit approvisionné consacre des ressources à votre conteneur ou votre base de données, le débit approvisionné vous est facturé même si vous n’exécutez pas de charges de travail.

* **Stockage consommé** : un tarif fixe vous est facturé pour la quantité totale de stockage (Go) utilisée pour les données et les index pour une heure donnée.

Le débit provisionné, facturé par [RU/s](request-units.md) (unités de requête par seconde), vous permet de lire ou d’écrire des données dans des conteneurs. Vous pouvez [approvisionner le débit sur une base de données ou sur un conteneur](set-throughput.md). En fonction des besoins de votre charge de travail, vous pouvez à tout moment réduire ou augmenter le débit. La tarification d’Azure Cosmos DB est flexible et elle est proportionnelle au débit que vous configurez sur une base de données ou un conteneur. Les valeurs minimales de débit et de stockage et les incréments de leur augmentation composent une gamme complète de prix et d’élasticité pour tous les types de clients, à petite ou à grande échelle. Chaque base de données ou conteneur est facturé sur une base horaire pour le débit approvisionné en unités de 100 RU/s, avec un minimum de 400 RU/s et pour le stockage consommé en Go. Contrairement au débit approvisionné, le stockage est facturé à la consommation. Autrement dit, vous n’êtes pas obligé de réserver le stockage à l’avance. Vous ne payez que le stockage que vous consommez.

Pour plus d'informations, consultez les pages [Tarification d'Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) et [Comprendre votre facture Azure Cosmos DB](understand-your-bill.md).

Le modèle de tarification dans Azure Cosmos DB est cohérent sur toutes les API. Pour plus d'informations, consultez [En quoi le modèle de tarification Azure Cosmos DB est rentable pour les clients](total-cost-ownership.md). Un débit minimal est nécessaire pour les bases de données et les conteneurs afin de respecter les contrats SLA ; il est possible d’augmenter ou de diminuer le débit provisionné par tranches de 100 RU/s.

Si vous déployez votre compte Azure Cosmos DB dans une région non gouvernementale aux États-Unis, le prix minimal du débit sur base de données et sur conteneur est d’environ 24 $/mois. Le tarif varie en fonction de la région. Pour connaître les dernières informations tarifaires, consultez la [page des tarifs Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Si votre charge de travail utilise plusieurs conteneurs, elle peut être optimisée pour limiter les coûts en appliquant un débit de niveau base de données. En effet, le débit de niveau base de données vous permet d’avoir autant de conteneurs que vous le souhaitez dans une base de données qui partage le débit entre les conteneurs. Le tableau suivant résume le débit approvisionné et les frais pour différentes entités :

|**Entité**  | **Débit minimal** |**Incréments** |**Étendue de l’approvisionnement** |
|---------|---------|---------|-------|
|Base de données    | 400 RU/s    | 100 RU/s   |Le débit est réservé pour la base de données et est partagé par les conteneurs au sein de la base de données |
|Conteneur     | 400 RU/s   | 100 RU/s  |Le débit est réservé pour un conteneur donné |

Comme l’indique le tableau précédent, le débit minimal dans Azure Cosmos DB commence à environ 24 $/mois. Si vous partez du débit minimal et l’augmentez peu à peu de façon à gérer vos charges de travail en production, les coûts augmenteront progressivement, par incréments d’environ 6 $/mois. Le tarif varie en fonction de la région. Pour connaître les dernières informations tarifaires, consultez la [page des tarifs Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Le modèle de tarification dans Azure Cosmos DB est flexible et l’augmentation ou la diminution du tarif est lissée lorsque vous augmentez ou que vous réduisez le débit.

## <a name="try-azure-cosmos-db-for-free"></a>Essayez gratuitement Azure Cosmos DB

Azure Cosmos DB offre plusieurs possibilités d’essai gratuit pour les développeurs. Ces options sont les suivantes :

* **Compte gratuit Azure** : Azure propose un [niveau gratuit](https://azure.microsoft.com/free/) avec 200 $ de crédit Azure pendant les 30 premiers jours et une quantité limitée de services gratuits pendant 12 mois. Pour plus d’informations, consultez la page [Compte Azure gratuit](../cost-management-billing/manage/avoid-charges-free-account.md). L’essai d’Azure Cosmos DB fait partie du compte gratuit Azure. Spécialement pour Azure Cosmos DB, ce compte gratuit offre 5 Go de stockage et 400 RU/s de débit approvisionné pour toute l’année. 

* **Essayez gratuitement Azure Cosmos DB** : Azure Cosmos DB propose une expérience à durée limitée pour essayer Azure Cosmos DB pour les comptes gratuits. Vous pouvez créer un compte Azure Cosmos DB, créer une base de données et des collections et exécuter un exemple d’application en utilisant les didacticiels et guides de démarrage rapide. Vous pouvez exécuter l’exemple d’application sans abonnement à un compte Azure ou avec votre carte de crédit. [Essai gratuit d'Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) offre Azure Cosmos DB pendant un mois, avec la possibilité de renouveler votre compte autant de fois que vous le souhaitez.

* **Émulateur Azure Cosmos DB** : l’émulateur Azure Cosmos DB fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. L’émulateur est disponible gratuitement et simule avec une grande fidélité le service cloud. L’émulateur Azure Cosmos DB vous permet de développer et de tester vos applications localement, sans créer d’abonnement Azure et sans frais. Vous pouvez développer vos applications en local à l’aide de l’émulateur avant de passer en production. Une fois que vous êtes satisfait du fonctionnement de l’application dans l’émulateur, vous pouvez commencer à utiliser le compte Azure Cosmos DB dans le cloud et faire des économies. Pour plus d’informations sur l’émulateur, consultez l’article [Utilisation d’Azure Cosmos DB pour le développement et le test](local-emulator.md) pour plus d’informations.

## <a name="pricing-with-reserved-capacity"></a>Tarification avec la capacité réservée

La [capacité réservée](cosmos-db-reserved-capacity.md) Azure Cosmos DB vous permet de faire des économies grâce au prépaiement des ressources Azure Cosmos DB sur un an ou trois ans. Vous pouvez considérablement réduire vos coûts avec les engagements à l’avance sur un ou trois ans et économiser entre 20 et 65 % par rapport à la tarification standard. La capacité réservée d’Azure Cosmos DB réservée permet de réduire les coûts en prépayant le débit approvisionné (RU/s) sur une période de un à trois ans, et vous bénéficiez d’une remise sur le débit approvisionné. 

La capacité réservée permet de bénéficier d’une remise, sans affecter l’état d’exécution de vos ressources Azure Cosmos DB. La capacité réservée est disponible pour toutes les API, ce qui comprend MongoDB, Cassandra, SQL, Gremlin et Table Azure, dans toutes les régions du monde. Vous retrouverez plus d’informations sur la capacité réservée dans l’article [Prépayer des ressources Azure Cosmos DB avec une capacité réservée](cosmos-db-reserved-capacity.md) et sur l’achat de capacité réservée dans le [portail Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

Vous retrouverez plus d’informations sur l’optimisation des coûts pour vos ressources Azure Cosmos DB dans les articles suivants :

* En savoir plus sur l’[optimisation pour le développement et le test](optimize-dev-test.md)
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des lectures et écritures](optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des requêtes](optimize-cost-queries.md)
* En savoir plus sur l’[optimisation du coût des comptes Cosmos sur plusieurs régions](optimize-cost-regions.md)
* En savoir plus sur la [capacité de réserve Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* En savoir plus sur l'[émulateur Azure Cosmos DB](local-emulator.md)
