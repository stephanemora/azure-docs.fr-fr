---
title: Présentation d’Azure Cosmos DB
description: En savoir plus sur Azure Cosmos DB. Cette base de données multi-modèle distribuée à l’échelle mondiale est conçue pour offrir une faible latence, une scalabilité élastique, une haute disponibilité et une prise en charge native des données NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: aad69a34cc27f341bec5beda0f52e2581538aaf9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278433"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bienvenue dans Azure Cosmos DB

Les applications actuelles doivent être hautement réactives et toujours en ligne. Pour obtenir une faible latence et une haute disponibilité, les instances de ces applications doivent être déployées dans des centres de données qui sont proches des utilisateurs. Les applications doivent répondre en temps réel à des changements importants d’utilisation aux heures de pointe, pour stocker des volumes croissants de données et pour rendre ces données disponibles aux utilisateurs en quelques millisecondes.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. D’un simple clic, Cosmos DB vous permet de mettre à l’échelle de façon élastique et indépendante le débit et le stockage dans autant de régions Azure que nécessaire à l’échelon mondial. Vous pouvez mettre à l’échelle de manière élastique le débit et le stockage, et bénéficier d’un accès rapide aux données (moins de 10 millisecondes) à l’aide de votre API favorite, notamment : SQL, MongoDB, Cassandra, Tables ou Gremlin. Cosmos DB offre des garanties en matière de débit, de latence, de disponibilité et de cohérence avec des [contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (SLA) complets, ce que n’offre aucun autre service de base de données.

Vous pouvez [essayer gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, sans frais ni engagement, ou utiliser le [niveau gratuit d’Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) pour obtenir un compte avec les 400 premières RU/s (unités de requête par seconde) et les 5 premiers Go de stockage gratuits.

> [!div class="nextstepaction"]
> [Essayer gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB est le service de base de données de Microsoft distribué à l’échelle mondiale proposant un scale-out élastique, une faible latence garantie, cinq modèles de cohérence et des contrats SLA complets garantis" border="false":::

## <a name="key-benefits"></a>Principaux avantages

### <a name="turnkey-global-distribution"></a>Une distribution mondiale clé en main

Cosmos DB vous permet de générer des applications hautement disponibles et très réactives dans le monde entier. Cosmos DB réplique en toute transparence vos données où que soient les utilisateurs, afin que ces derniers puissent interagir avec le réplica des données le plus proche.

Cosmos DB permet d’ajouter ou de supprimer des régions Azure dans votre compte Cosmos à tout moment, via un clic sur un bouton. Cosmos DB réplique de façon continue vos données dans toutes les régions associées à votre compte Cosmos, tandis que votre application reste hautement disponible grâce aux fonctionnalités *multi-résidence* du service. Pour plus d’informations, consultez l’article sur la [distribution mondiale](distribute-data-globally.md).

### <a name="always-on"></a>Always On

En vertu d’une profonde intégration dans l’infrastructure Azure et d’une [réplication d’écriture multi-région transparente](global-dist-under-the-hood.md), Cosmos DB offre une [haute disponibilité à 99,999 %](high-availability.md) pour les lectures et les écritures. Cosmos DB permet également d’appeler par programmation (ou via le portail) le basculement régional de votre compte Cosmos. Cette fonctionnalité est la garantie que votre application basculera en cas de défaillance au niveau régional.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Extensibilité élastique du débit et du stockage à l’échelon mondial

Conçu avec un partitionnement horizontal transparent et une réplication d’écriture multi-région, Cosmos DB offre une extensibilité élastique sans précédent pour vos écritures et lectures, partout dans le monde. Vous pouvez passer de façon élastique de milliers à des centaines de millions de requêtes par seconde dans le monde, en un seul appel d’API, et payer uniquement le débit (et le stockage) dont vous avez besoin. Cette fonctionnalité vous aide à gérer les pics inattendus dans vos charges de travail sans devoir effectuer un surprovisionnement. Pour plus d’informations, consultez les articles sur le [partitionnement dans Cosmos DB](partitioning-overview.md), le [débit provisionné sur les conteneurs et les bases de données](set-throughput.md) et la [mise à l’échelle du débit provisionné dans le monde entier](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Faible latence garantie, au 99e centile, partout dans le monde

À l’aide de Cosmos DB, vous pouvez générer des applications hautement réactives à l’échelle de la planète. Avec son protocole novateur de réplication d’écriture multi-région et sans verrou, ainsi que son [moteur de base de données optimisé pour l’écriture](index-policy.md), Cosmos DB garantit des latences inférieures à 10 millisecondes pour les lectures et les écritures (indexées), au 99e centile, dans le monde entier. Cette fonctionnalité permet l’ingestion soutenue de données et des requêtes très rapides pour des applications hautement réactives.

### <a name="precisely-defined-multiple-consistency-choices"></a>Plusieurs choix de cohérence bien définis

Quand vous créez des applications distribuées à l’échelle mondiale dans Cosmos DB, vous n’avez plus besoin de faire de [compromis extrêmes entre la cohérence, la disponibilité, la latence et le débit](consistency-levels-tradeoffs.md). Le protocole de réplication d’écriture multi-région de Cosmos DB a été soigneusement conçu pour offrir [cinq choix de cohérence bien définis](consistency-levels.md) - *fort*, *obsolescence limitée*, *session*, *préfixe cohérent* et *éventuel* : pour fournir à votre application distribuée à l’échelle mondiale un modèle de programmation intuitif, à faible latence et à haute disponibilité.

### <a name="no-schema-or-index-management"></a>Aucune gestion des schémas ou des index

Pour les applications distribuées à l’échelle mondiale, il est particulièrement difficile de garder le schéma de la base de données et les index synchronisés avec le schéma d’une application. Avec Cosmos DB, vous n’avez pas besoin de gérer les schémas ni les index. Le moteur de base de données est entièrement sans schéma.  Comme aucune gestion de schéma ni d’index n’est nécessaire, vous n’avez pas à vous soucier des temps d’arrêt des applications lors de la migration des schémas. Cosmos DB [indexe automatiquement toutes les données](index-policy.md) et envoie les requêtes rapidement.

### <a name="battle-tested-database-service"></a>Service de base de données à toute épreuve

Cosmos DB est un service de base d’Azure. Depuis près d’une décennie, Cosmos DB est utilisé par de nombreux produits Microsoft pour des applications stratégiques à l’échelle mondiale, notamment Skype, Xbox, Microsoft 365, Azure et bien d’autres. Aujourd’hui, Cosmos DB est un des services à la croissance la plus rapide sur Azure, utilisé par de nombreux clients externes et applications stratégiques qui nécessitent une mise à l’échelle élastique, une distribution mondiale clé en main, une réplication d’écriture multi-région pour une faible latence et une haute disponibilité, à la fois en lecture et en écriture.

### <a name="ubiquitous-regional-presence"></a>Omniprésence régionale

Cosmos DB est disponible dans toutes les régions Azure du monde, dont plus de 54 régions dans le cloud public, [Azure Chine 21Vianet](https://www.azure.cn/en-us/), Azure Allemagne, Azure Government et Azure Government for Department of Defense (DoD). Consultez [Haute disponibilité avec Azure Cosmos DB](high-availability.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Sécurisé par défaut et adapté au monde de l’entreprise

Cosmos DB est certifié pour un [vaste éventail de normes de conformité](compliance.md). En outre, toutes les données dans Cosmos DB sont chiffrées au repos et en mouvement. Cosmos DB fournit une autorisation de niveau ligne et respecte des normes de sécurité strictes.

### <a name="significant-tco-savings"></a>Économies substantielles sur le coût total de possession

Cosmos DB étant un service complètement managé, vous n’avez plus à gérer ni à effectuer des déploiements complexes de centre de données et des mises à niveau de vos logiciels de base de données. Vous payez le support, les licences ou l’exploitation ou devez provisionner votre base de données pour les pics de charge de travail. Pour plus d’informations, consultez [Optimiser les coûts avec Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Contrats de niveau de service complets à la pointe du secteur

Cosmos DB est le seul service à proposer les [meilleurs contrats SLA complets du secteur](https://azure.microsoft.com/support/legal/sla/cosmos-db/) avec une haute disponibilité à 99,999 %, une latence au 99e centile en lecture et en écriture, ainsi qu’un débit et une cohérence garantis.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Analytique opérationnelle distribuée à l’échelle mondiale et IA avec Apache Spark intégré en mode natif

Vous pouvez exécuter [Spark](spark-connector.md) directement sur les données stockées dans Cosmos DB. Cette fonctionnalité vous permet d’effectuer des analyses opérationnelles de faible latence à l’échelle mondiale sans impact sur les charges de travail transactionnelles survenant directement sur Cosmos DB. Pour plus d’informations, consultez [Analytique opérationnelle distribuée à l’échelle mondiale](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Développer des applications sur Cosmos DB à l’aide d’API OSS (Open Source Software) populaires

Cosmos DB propose plusieurs API pour exploiter les données stockées dans votre base de données Cosmos. Par défaut, [vous pouvez utiliser SQL](how-to-sql-query.md) (API de base) pour interroger votre base de données Cosmos. Cosmos DB implémente aussi des API pour [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) et [Stockage Table Azure](table-introduction.md). Vous pouvez faire pointer les pilotes (et outils) clients pour les API NoSQL couramment utilisées (MongoDB, Cassandra, Gremlin, etc.) directement vers votre base de données Cosmos. En prenant en charge les protocoles de transmission des API NoSQL couramment utilisées, Cosmos DB vous permet de :

* Migrer facilement votre application vers Cosmos DB tout en conservant des parties importantes de la logique d’application.
* Préserver la mobilité de votre application et garder votre indépendance vis-à-vis des fournisseurs.
* Obtenir un service cloud complètement managé assorti des meilleurs contrats de niveau de service financés pour les API NoSQL courantes. 
* Profiter d’une mise à l’échelle élastique du débit et du stockage approvisionnés pour vos bases de données selon vos besoins, et payer uniquement le débit et le stockage dont vous avez besoin. Vous réaliserez ainsi des économies considérables.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Les solutions qui profitent des avantages de Azure Cosmos DB

Toutes les [applications web, mobiles, IoT ou de jeux](use-cases.md) qui ont besoin de traiter des volumes importants de données, de lectures et d’écritures à une [échelle mondiale](distribute-data-globally.md) avec des temps de réponse proches du temps réel pour une grande variété de données peuvent profiter des garanties de Cosmos DB en termes de [haute disponibilité](https://azure.microsoft.com/support/legal/sla/cosmos-db/), de haut débit, de faible latence et de cohérence ajustable. En savoir plus sur la façon dont Azure Cosmos DB peut s’appliquer à l’[IoT et la télématique](use-cases.md#iot-and-telematics), à la [vente au détail et au marketing](use-cases.md#retail-and-marketing), aux [jeux](use-cases.md#gaming) et aux [applications web et mobiles](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les concepts de base de Cosmos DB de [distribution mondiale clé en main](distribute-data-globally.md), de [partitionnement](partitioning-overview.md) et de [débit provisionné](request-units.md).

Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Prise en main de l’API SQL Azure Cosmos DB](create-sql-api-dotnet.md)
* [Bien démarrer avec l’API Azure Cosmos DB pour MongoDB](create-mongodb-nodejs.md)
* [Prise en main de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md)
* [Prise en main de l’API Gremlin Azure Cosmos DB](create-graph-dotnet.md)
* [Prise en main de l’API Table Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
