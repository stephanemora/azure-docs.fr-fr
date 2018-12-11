---
title: Présentation d’Azure Cosmos DB
description: En savoir plus sur Azure Cosmos DB. Cette base de données multi-modèle distribuée à l’échelle mondiale est conçue pour offrir une faible latence, une scalabilité élastique, une haute disponibilité et une prise en charge native des données NoSQL.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 89f5ae27e7ce3ec3155fd3fdbf42bb0f4322aa10
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835061"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bienvenue dans Azure Cosmos DB

Azure Cosmos DB est un service de base de données multimodèle mondialement distribué de Microsoft. En un clic, le service Azure Cosmos DB vous permet de faire évoluer en toute flexibilité et de façon indépendante le débit et le stockage sur n’importe quel nombre de régions géographiques Azure. Il offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des [contrats SLA complets](https://aka.ms/acdbsla), ce qu’aucun autre service de base de données ne peut offrir. Vous pouvez [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, libre de tous frais et engagements.

> [!div class="nextstepaction"]
> [Essayer gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB est le service de base de données distribué mondialement de Microsoft qui propose une augmentation de la taille des instances, une faible latence, cinq modèles de cohérence et des contrats SLA offrant des garanties complètes](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Fonctionnalités clés
En tant que service de base de données multi-model distribué, Azure Cosmos DB permet de créer aisément des applications évolutives et très réactives, à l’échelle globale :

* **Une distribution mondiale clé en main**
    * Vous pouvez [distribuer vos données](distribute-data-globally.md) à un nombre illimité de [régions Azure](https://azure.microsoft.com/regions/), d’un [simple clic](tutorial-global-distribution-sql-api.md). Cela vous permet de placer vos données là où se trouvent vos utilisateurs, assurant ainsi la latence la plus faible possible pour vos clients. 
    * À l’aide des API multihébergement d’Azure Cosmos DB, l’application sait où se trouve la région la plus proche et envoie les requêtes au centre de données le plus proche. Tout ceci est possible sans aucune modification de configuration. Vous définissez votre région d’écriture et autant de régions de lecture que vous souhaitez, le reste est géré pour vous.
    * Lorsque vous ajoutez et supprimez des régions dans votre base de données Azure Cosmos DB, votre application ne doit pas être redéployée et continue d’être hautement disponible grâce à la fonctionnalité d’API multihébergement.

* **Plusieurs modèles de données et API populaires pour la consultation et l’interrogation des données**
    * Le modèle de données atome-enregistrement-séquence (ARS) sur lequel repose Azure Cosmos DB prend en charge en mode natif plusieurs modèles de données, y compris, mais sans s’y limiter les documents, les graphes, les valeurs-clés, les tables et les modèles de données de familles de colonnes.
    * Les API pour les modèles suivants sont pris en charge avec les SDK et sont disponibles dans plusieurs langages :
        * [API SQL](sql-api-introduction.md) : moteur de base de données JSON sans schéma avec de riches fonctions d’interrogation SQL.
        * [API MongoDB](mongodb-introduction.md) : service *MongoDB en tant que service* hautement évolutif, alimenté par la plateforme Azure Cosmos DB. Compatible avec les bibliothèques, pilotes, outils et applications MongoDB existants.
        * [API Cassandra](cassandra-introduction.md) : service Cassandra en tant que service mondialement distribué, alimenté par la plateforme Azure Cosmos DB. Compatible avec les bibliothèques, pilotes, outils et applications [Apache Cassandra](https://cassandra.apache.org/) existants.
        * [API Gremlin](graph-introduction.md) : service de base de données de graphe évolutif horizontalement, entièrement managé, qui facilite la création et l’exécution d’applications qui fonctionnent avec des jeux de données hautement connectés prenant en charge les API Open Gremlin (d’après la [spécification Apache TinkerPop](http://tinkerpop.apache.org/), Apache Gremlin).
        * [API de table](table-introduction.md): service de base de données valeur-clé créé pour offrir des fonctionnalités Premium (par exemple, indexation automatique, faible latence garantie, distribution globale) aux applications de stockage de table Azure sans apporter de modifications aux applications.
        * Des modèles de données et des API supplémentaires seront bientôt disponibles.

* **Ajuster le débit et le stockage de façon élastique et à la demande, dans le monde entier**
    * Faites évoluer facilement le débit de la base de données avec une granularité [par seconde](request-units.md) et modifiez-le à tout moment. 
    * Mettez à l’échelle la taille de stockage de façon [automatique et transparente](partition-data.md) pour gérer vos besoins existants et futurs.

* **Créer des applications hautement réactives et stratégiques**
    * Azure Cosmos DB garantit une faible latence de bout en bout au 99e centile à ses clients. 
    * Pour un document de 1 Ko, Cosmos DB garantit une latence de bout en bout des lectures inférieure à 10 ms et des écritures indexées inférieure à 10 ms au 99e centile, dans la même région Azure. Les latences médianes sont nettement plus faibles (inférieures à 5 ms).

* **Assurer une disponibilité en continu**
    * Contrat SLA de disponibilité à 99,99 % pour tous les comptes de base de données à une seule région, ainsi qu’une disponibilité de lecture à 99,999 % pour tous les comptes de base de données à plusieurs régions.
    * Déployez dans n’importe quel nombre de [régions Azure](https://azure.microsoft.com/regions) pour bénéficier d’une plus haute disponibilité et de meilleures performances.
    * Définissez de manière dynamique des priorités pour les régions et [simulez une panne](high-availability.md) d’une ou plusieurs régions avec comme garantie l’absence de perte de données pour tester la disponibilité de bout en bout pour l’application entière (au-delà de la base de données uniquement). 

* **Écrire des applications distribuées mondialement, de la bonne façon**
    * Les cinq [modèles de cohérence](consistency-levels.md) bien définis, pratiques et intuitifs fournissent un spectre de cohérence allant d’une cohérence forte de type SQL jusqu’à une cohérence éventuelle plus souple de type NoSQL, comprenant toutes les cohérences intermédiaires. 
  
* **Garantie de remboursement**
    * [Contrats de niveau de service](https://aka.ms/acdbsla) complets, parmi les meilleurs du secteur, s’appuyant sur une garantie financière pour la disponibilité, la latence, le débit et la cohérence de vos données critiques. 

* **Aucune gestion de schéma ou d’index de base de données**
    * Itérez rapidement le schéma de votre application sous vous soucier du schéma de la base de données et/ou de la gestion de l’indexation.
    * Le moteur de base de données Azure Cosmos DB est entièrement indépendant du schéma : il indexe automatiquement toutes les données qu’il reçoit sans schéma ni index et répond aux requêtes de façon ultrarapide. 

* **Faible coût total de possession**
    * Cinq à dix fois plus économique qu’une solution non managée ou qu’une solution NoSQL en local.
    * Solution trois fois moins chère que AWS DynamoDB ou Google Spanner.

## <a name="capability-comparison"></a>Comparaison des fonctionnalités

Azure Cosmos DB fournit les meilleures fonctionnalités des bases de données classiques relationnelles et non relationnelles.

| Fonctionnalités | Bases de données relationnelles   | Bases de données non relationnelles (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Diffusion mondiale | Non  | Non  | Oui, la distribution clé en main est disponible avec les API multihébergement dans plus de 30 régions.|
| Mise à l’échelle horizontale | Non  | Oui | Oui, vous pouvez mettre le stockage et le débit à l’échelle indépendamment. | 
| Garanties de latence | Non  | Oui | Oui, la latence de 99 % des lectures est inférieure à 10 ms et celle des écritures est inférieure à 10 ms | 
| Haute disponibilité | Non  | Oui | Oui, Cosmos DB est toujours disponible, respecte les compromis PACELC, et permet des basculements manuels et automatiques.|
| Modèle de données + API | Relationnel + SQL | Multi-modèle + API OSS | Multi-model + SQL + OSS API |
| Contrats SLA | Oui | Non  | Oui, les contrats SLA sont complets en termes de latence, de débit, de cohérence et de disponibilité. |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Les solutions qui profitent des avantages de Azure Cosmos DB

N’importe quelle [application web, mobile, IoT ou de jeux](use-cases.md) qui doit gérer des volumes importants de données, de lectures et d’écritures sur une échelle [globale](distribute-data-globally.md) avec un temps de réponse quasi en temps réel pour une grande variété de données profite de la haute disponibilité, du débit élevé, de la faible latence et de la cohérence ajustable [garanties](https://azure.microsoft.com/support/legal/sla/cosmos-db/) d’Azure Cosmos DB. En savoir plus sur la façon dont Azure Cosmos DB peut s’appliquer à l’[IoT et la télématique](use-cases.md#iot-and-telematics), à la [vente au détail et au marketing](use-cases.md#retail-and-marketing), aux [jeux](use-cases.md#gaming) et [applications Web et mobiles](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Étapes suivantes
Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Prise en main de l’API SQL Azure Cosmos DB](create-sql-api-dotnet.md)
* [Prise en main de l’API MongoDB Azure Cosmos DB](create-mongodb-nodejs.md)
* [Prise en main de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md)
* [Prise en main de l’API Gremlin Azure Cosmos DB](create-graph-dotnet.md)
* [Prise en main de l’API Table Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
