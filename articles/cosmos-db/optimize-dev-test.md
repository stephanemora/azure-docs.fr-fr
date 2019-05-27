---
title: Optimisation pour le développement et le test dans Azure Cosmos DB
description: Cet article explique comment Azure Cosmos DB propose plusieurs options pour développer et tester gratuitement le service.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: f9cb18b66def144b84de708351743832d1831fbf
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967262"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimiser les coûts de développement et de test dans Azure Cosmos DB

Cet article décrit les différentes options pour utiliser gratuitement Azure Cosmos DB pour le développement et le test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Émulateur Azure Cosmos DB (version téléchargeable localement)

L’[émulateur Azure Cosmos DB](local-emulator.md) est une version téléchargeable en local qui simule le service cloud Azure Cosmos DB. Vous pouvez écrire et tester du code qui utilise les API Azure Cosmos DB même si vous ne disposez d’aucune connexion réseau, et sans frais. L’émulateur Azure Cosmos DB fournit un environnement local qui reproduit de façon fidèle le service Azure Cosmos DB à des fins de développement. Vous pouvez développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes prêt à déployer votre application dans le cloud, mettez à jour la chaîne de connexion pour vous connecter au point de terminaison Azure Cosmos DB dans le cloud. Aucune autre modification n’est nécessaire. Vous pouvez aussi [configurer un pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB](tutorial-setup-ci-cd.md) dans Azure DevOps pour effectuer des tests. Pour vous lancer, consultez l’article [Émulateur Azure Cosmos DB](local-emulator.md).

## <a name="try-azure-cosmos-db-for-free"></a>Essayez gratuitement Azure Cosmos DB

[Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) : une expérience gratuite qui vous permet de créer des bases de données et des collections et de tester gratuitement Azure Cosmos DB dans le cloud. Vous n’avez pas besoin de vous inscrire sur Azure ni de payer quoi que ce soit. Les comptes d’essai Azure Cosmos DB sont disponibles pour une durée limitée, actuellement de 30 jours. Vous pouvez les renouveler à tout moment. Les comptes d’essai Azure Cosmos DB permettent de facilement évaluer Azure Cosmos DB, de générer et de tester une application avec les démarrages rapides ou les didacticiels. Vous pouvez créer une démonstration ou effectuer des tests sans payer quoi que ce soit. Avec les comptes gratuits Azure Cosmos DB, vous pouvez évaluer les fonctionnalités Premium d’Azure Cosmos DB gratuitement, par exemple la distribution globale, les contrats SLA et les modèles de cohérence. Vous pouvez créer une base de données avec un maximum de 25 conteneurs Azure Cosmos et 10 000 RU/s de débit. Vous pouvez exécuter l’exemple d’application sans abonnement à un compte Azure ou avec votre carte de crédit. Avec l’essai gratuit d’Azure Cosmos DB, vous pouvez créer un compte Azure Cosmos sur plusieurs régions et exécuter une application en quelques minutes. Pour vous lancer, consultez la page [Essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Compte gratuit Azure

Azure Cosmos DB est inclus dans le [compte gratuit Azure](https://azure.microsoft.com/free), qui offre gratuitement des crédits et des ressources Azure pendant un certain temps. Spécialement pour Azure Cosmos DB, ce compte gratuit offre 5 Go de stockage et 400 RU/s de débit approvisionné pour toute l’année. Cette expérience permet aux développeurs de facilement tester les fonctionnalités d’Azure Cosmos DB ou de les intégrer gratuitement à d’autres services Azure. Avec un compte gratuit Azure, vous bénéficiez d’un crédit de 200 $ à dépenser dans les 30 premiers jours. Rien ne vous sera facturé, même si vous commencez à utiliser les services, tant que vous n’avez pas procédé à une mise à niveau. Pour commencer, consultez la page [Compte gratuit Azure](https://azure.microsoft.com/free).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez commencer à utiliser l’émulateur ou les comptes Azure Cosmos DB gratuits avec les articles suivants :

* En savoir plus sur l’[optimisation pour le développement et le test](optimize-dev-test.md)
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des lectures et écritures](optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des requêtes](optimize-cost-queries.md)
* En savoir plus sur l’[optimisation du coût des comptes Azure Cosmos sur plusieurs régions](optimize-cost-regions.md)

