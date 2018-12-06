---
title: Provisionner le débit pour Azure Cosmos DB
description: Découvrez comment définir un débit provisionné pour vos conteneurs et bases de données Azure Cosmos DB.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: a97032344b904442ed3606c6297251578c3b4ff7
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263891"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Provisionner le débit sur les conteneurs et les bases de données Azure Cosmos

Une base de données Azure Cosmos est une unité de gestion pour un ensemble de conteneurs. Une base de données se compose d’un ensemble de conteneurs sans schéma. Un conteneur Azure Cosmos est l’unité d’extensibilité pour le stockage et le débit. Un conteneur est partitionné horizontalement sur un ensemble de machines au sein d’une région Azure et réparti entre toutes les régions Azure associées à votre compte Azure Cosmos.

Azure Cosmos DB vous permet de configurer le débit selon deux granularités : **conteneurs Azure Cosmos** et **bases de données Azure Cosmos**.

# <a name="setting-throughput-on-a-azure-cosmos-container"></a>Définition du débit sur un conteneur Azure Cosmos  

Le débit provisionné sur un conteneur Azure Cosmos est exclusivement réservé au conteneur. Le conteneur reçoit en permanence le débit provisionné. Le débit provisionné sur un conteneur est directement associé à des contrats SLA. Pour configurer le débit sur un conteneur, consultez [Provisionnement du débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).

Définir le débit provisionné sur un conteneur correspond à l’option la plus utilisée. S'il vous est possible de mettre à l'échelle le débit d'un conteneur de façon élastique en provisionnant une quantité de débit (unités de requête), vous ne pouvez pas spécifier de manière sélective le débit des partitions logiques. Si la charge de travail d'une partition logique consomme plus que le débit alloué à cette partition spécifique, vos opérations sont limitées en termes de débit. En cas de limitation, vous pouvez augmenter le débit pour l’intégralité du conteneur ou retenter l’opération. Pour plus d’informations sur le partitionnement, consultez [Partitions logiques](partition-data.md).

Il vous est recommandé de configurer le débit au niveau de la granularité du conteneur pour garantir les performances de ce conteneur.

Le débit provisionné sur un conteneur Azure Cosmos est uniformément réparti entre toutes les partitions logiques du conteneur. Dans la mesure où une ou plusieurs partitions logiques d’un conteneur sont hébergées par une partition de ressources, les partitions physiques appartiennent exclusivement au conteneur et prennent en charge le débit provisionné sur ce conteneur. L’illustration suivante montre comment une partition de ressources héberge une ou plusieurs partitions logiques d’un conteneur :

![Partition de ressources](./media/set-throughput/resource-partition.png)

# <a name="setting-throughput-on-a-azure-cosmos-database"></a>Définition du débit sur une base de données Azure Cosmos

Quand vous provisionnez le débit sur une base de données Azure Cosmos, il est partagé entre tous les conteneurs de la base de données, sauf si vous avez spécifié un débit provisionné sur des conteneurs spécifiques. Le partage de débit de la base de données entre ses conteneurs est analogue à l’hébergement d’une base de données sur un cluster de machines. Tous les conteneurs d’une base de données partageant les ressources disponibles sur une machine, vous ne pouvez pas prévoir les performances d'un conteneur spécifique. Pour configurer le débit sur une base de données, consultez [Configurer le débit provisionné sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

Définir le débit sur une base de données Azure Cosmos vous permet de veiller à ce que vous receviez en permanence le débit provisionné. Tous les conteneurs d’une base de données partageant le débit provisionné, Azure Cosmos DB n’offre pas de garantie de débit prévisible pour un conteneur donné de cette base de données. La portion de débit qu'un conteneur spécifique peut recevoir dépend de ce qui suit :

* Nombre de conteneurs
* Choix des clés de partition pour différents conteneurs
* Distribution de la charge de travail entre les différentes partitions logiques des conteneurs 

Il est recommandé de configurer le débit sur une base de données lorsque vous souhaitez partager ce débit entre plusieurs conteneurs, et ne pas le dédier à un conteneur spécifique. Voici quelques exemples de situations où il est préférable de provisionner le débit au niveau de la base de données :

* Partager le débit provisionné d'une base de données dans un ensemble de conteneurs est utile dans le cadre d'une application mutualisée. Chaque utilisateur peut être représenté par un conteneur Azure Cosmos distinct.

* Partager le débit provisionné d’une base de données sur un ensemble de conteneurs est utile quand vous migrez une base de données NoSQL (par exemple, MongoDB, Cassandra) hébergée à partir d’un cluster de machines virtuelles ou de serveurs physiques locaux vers Azure Cosmos DB. Vous pouvez considérer le débit provisionné configuré sur votre base de données Azure Cosmos en tant qu’équivalent logique (mais plus rentable et élastique) de la capacité de calcul de votre cluster MongoDB ou Cassandra.  

À un moment donné, le débit alloué à un conteneur au sein d’une base de données est réparti entre toutes les partitions logiques de ce conteneur. En présence de conteneurs partageant un débit provisionné sur une base de données, vous ne pouvez pas appliquer de manière sélective le débit à un conteneur ou une partition logique spécifique. Si la charge de travail d'une partition logique consomme plus que le débit alloué à une partition logique spécifique, vos opérations sont limitées en termes de débit. En cas de limitation, vous pouvez augmenter le débit pour l’intégralité du conteneur ou retenter l’opération. Pour plus d’informations sur le partitionnement, consultez [Partitions logiques](partition-data.md).

Plusieurs partitions logiques partageant le débit provisionné sur une base de données peuvent être hébergées sur une partition de ressources unique. Si une seule partition logique appartenant à un conteneur est toujours limitée dans une partition de ressources, les partitions logiques « L » des conteneurs « C » partageant le débit provisionné d'une base de données peuvent être mappées et hébergées sur les partitions physiques « R ». L’illustration suivante montre comment une partition de ressources peut héberger une ou plusieurs partitions logiques appartenant à différents conteneurs au sein d’une base de données :

![Partition de ressources](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-azure-cosmos-database-and-a-container"></a>Définition du débit sur une base de données et un conteneur Azure Cosmos

Vous pouvez combiner les deux modèles, à savoir, provisionner le débit sur la base de données et le conteneur. L’exemple suivant montre comment provisionner le débit sur une base de données et un conteneur Azure Cosmos :

* Vous pouvez créer une base de données Azure Cosmos nommée « Z » avec le débit provisionné des unités de requête « K ». 
* Créez ensuite cinq conteneurs nommés A, B, C, D et E dans la base de données.
* Vous pouvez explicitement configurer des unités de requête « P » de débit provisionné sur le conteneur « B ».
* Le débit des unités de requête « K » est partagé entre les quatre conteneurs : A, C, D et E. La quantité exacte de débit disponible pour A, C, D ou E varie et il n’existe pas de contrat SLA correspondant au débit de chaque conteneur individuel.
* Le conteneur « B » est assuré de bénéficier en permanence du débit des unités de requête « P » et est associé à des contrats SLA.

## <a name="comparison-of-models"></a>Comparaison des modèles

|**Quota**  |**Débit provisionné sur une base de données**  |**Débit provisionné sur un conteneur**|
|---------|---------|---------|
|Unités de requête minimales |400 |400|
|Unités de requête minimales par conteneur|100|400|
|Unités de requête minimales requises pour consommer 1 Go de stockage|40|40|
|Unités de requête maximales|Illimitées, sur la base de données|Illimitées, sur le conteneur|
|Unités de requête allouées/disponibles sur un conteneur spécifique|Aucune garantie. Les unités de requête allouées à un conteneur donné dépendent de propriétés telles que le choix des clés de partition des conteneurs partageant le débit, la distribution de la charge de travail, le nombre de conteneurs. |Toutes les unités de requête configurées sur le conteneur sont exclusivement réservées à ce conteneur.|
|Stockage maximal pour un conteneur|Illimité|Illimité|
|Débit maximal par partition logique d’un conteneur|10 000 RU/s|10 000 RU/s|
|Débit maximal (données + index) par partition logique d’un conteneur|10 Go|10 Go|

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [partitions logiques](partition-data.md)
* En savoir plus sur le [provisionnement du débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md)
* En savoir plus sur le [provisionnement du débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md)

