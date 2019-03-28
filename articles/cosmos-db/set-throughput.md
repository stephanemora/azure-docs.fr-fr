---
title: Provisionner le débit sur les conteneurs et les bases de données Azure Cosmos
description: Découvrez comment définir un débit provisionné pour vos conteneurs et bases de données Azure Cosmos.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: andrl
ms.openlocfilehash: 8335a235de708227136400f3af8fa7b4d0a52e29
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520902"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Approvisionner le débit sur les conteneurs et les bases de données

Une base de données Azure Cosmos est une unité de gestion pour un ensemble de conteneurs. Une base de données se compose d’un ensemble de conteneurs sans schéma. Un conteneur Azure Cosmos est l’unité d’extensibilité pour le stockage et le débit. Un conteneur est partitionné horizontalement sur un ensemble de machines au sein d’une région Azure et réparti entre toutes les régions Azure associées à votre compte Azure Cosmos.

Avec Azure Cosmos DB, vous pouvez configurer le débit selon deux niveaux de granularité :
 
- Conteneurs Cosmos Azure
- Bases de données Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Définir le débit sur un conteneur  

Le débit provisionné sur un conteneur Azure Cosmos est exclusivement réservé au conteneur. Le conteneur reçoit en permanence le débit provisionné. Le débit provisionné sur un conteneur est directement associé à des contrats SLA. Pour configurer le débit sur un conteneur, consultez [Provisionnement du débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).

Définir le débit provisionné sur un conteneur correspond à l’option la plus utilisée. Vous pouvez mettre à l’échelle le débit pour un conteneur de manière élastique en provisionnant une quantité quelconque de débit à l’aide d’unités de requête (RU). Mais vous ne pouvez pas spécifier de manière sélective le débit pour les partitions logiques. 

Si la charge de travail d'une partition logique consomme plus que le débit alloué à cette partition spécifique, vos opérations sont limitées en termes de débit. En cas de limitation, vous pouvez augmenter le débit pour l’intégralité du conteneur ou retenter les opérations. Pour plus d’informations sur le partitionnement, consultez [Partitions logiques](partition-data.md).

Il vous est recommandé de configurer le débit au niveau de la granularité du conteneur pour garantir les performances de ce conteneur.

Le débit provisionné sur un conteneur Azure Cosmos est uniformément réparti entre toutes les partitions logiques du conteneur. Dans la mesure où une ou plusieurs partitions logiques d’un conteneur sont hébergées par une partition physique, les partitions physiques appartiennent exclusivement au conteneur et prennent en charge le débit provisionné sur ce conteneur. 

L’illustration suivante montre comment une partition physique héberge une ou plusieurs partitions logiques d’un conteneur :

![Partition physique](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Définir le débit sur une base de données

Quand vous provisionnez le débit sur une base de données Azure Cosmos, il est partagé entre tous les conteneurs de la base de données. Sauf si vous avez spécifié un débit provisionné sur des conteneurs spécifiques. Le partage de débit de la base de données entre ses conteneurs est analogue à l’hébergement d’une base de données sur un cluster de machines. Tous les conteneurs d’une base de données partageant les ressources disponibles sur une machine, vous ne pouvez pas prévoir les performances d'un conteneur spécifique. Pour configurer le débit sur une base de données, consultez [Configurer le débit provisionné sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

Définir le débit sur une base de données Azure Cosmos vous permet de veiller à ce que vous receviez en permanence le débit provisionné. Tous les conteneurs d’une base de données partageant le débit provisionné, Azure Cosmos DB n’offre pas de garantie de débit prévisible pour un conteneur donné de cette base de données. La portion de débit qu'un conteneur spécifique peut recevoir dépend de ce qui suit :

* Nombre de conteneurs.
* Choix des clés de partition pour différents conteneurs.
* Distribution de la charge de travail entre les différentes partitions logiques des conteneurs 

Il est recommandé de configurer le débit sur une base de données lorsque vous souhaitez partager ce débit entre plusieurs conteneurs, et ne pas le dédier à un conteneur spécifique. 

Les exemples suivants démontrent des situations où il est préférable de provisionner le débit au niveau de la base de données :

* Partager le débit provisionné d'une base de données dans un ensemble de conteneurs est utile dans le cadre d'une application mutualisée. Chaque utilisateur peut être représenté par un conteneur Azure Cosmos distinct.

* Partager le débit provisionné d’une base de données sur un ensemble de conteneurs est utile quand vous migrez une base de données NoSQL (par exemple, MongoDB ou Cassandra) hébergée sur un cluster de machines virtuelles ou de serveurs physiques locaux vers Azure Cosmos DB. Vous pouvez considérer le débit provisionné configuré sur votre base de données Azure Cosmos en tant qu’équivalent logique (mais plus rentable et élastique) de la capacité de calcul de votre cluster MongoDB ou Cassandra.  

Tous les conteneurs créés à l’intérieur d’une base de données avec un débit provisionné doivent être créés avec une clé de partition. À un moment donné, le débit alloué à un conteneur au sein d’une base de données est réparti entre toutes les partitions logiques de ce conteneur. En présence de conteneurs partageant un débit provisionné sur une base de données, vous ne pouvez pas appliquer de manière sélective le débit à un conteneur ou une partition logique spécifique. 

Si la charge de travail d'une partition logique consomme plus que le débit alloué à une partition logique spécifique, vos opérations sont limitées en termes de débit. En cas de limitation, vous pouvez augmenter le débit pour l’intégralité du conteneur ou retenter les opérations. Pour plus d’informations sur le partitionnement, consultez [Partitions logiques](partition-data.md).

Plusieurs partitions logiques partageant le débit provisionné sur une base de données peuvent être hébergées sur une partition physique unique. Si une seule partition logique appartenant à un conteneur est toujours limitée dans une partition physique, les partitions logiques « L » des conteneurs « C » partageant le débit provisionné d’une base de données peuvent être mappées et hébergées sur les partitions physiques « R ». 

L’illustration suivante montre comment une partition physique peut héberger une ou plusieurs partitions logiques, appartenant à différents conteneurs, au sein d’une base de données :

![Partition physique](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Définir le débit sur une base de données et un conteneur

Vous pouvez combiner les deux modèles. Provisionner le débit sur la base de données et le conteneur est autorisé. L’exemple suivant montre comment provisionner le débit sur une base de données et un conteneur Azure Cosmos :

* Vous pouvez créer une base de données Azure Cosmos nommée « Z » avec le débit provisionné des unités de requête « K ». 
* Créez ensuite cinq conteneurs nommés A, B, C, D et E dans la base de données.
* Vous pouvez explicitement configurer des unités de requête « P » de débit provisionné sur le conteneur nommé B.
* Le débit des unités de requête « K » est partagé entre les quatre conteneurs : A, C, D et E. La quantité exacte de débit disponible pour A, C, D ou E varie. Il n’existe pas de contrat SLA correspondant au débit de chaque conteneur individuel.
* Le conteneur nommé B est assuré de bénéficier en permanence du débit des unités de requête « P ». Il est associé à des contrats SLA.

## <a name="update-throughput-on-a-database-or-a-container"></a>Débit de mise à jour sur une base de données ou un conteneur

Une fois que vous créez un conteneur Azure Cosmos ou une base de données, vous pouvez mettre à jour le débit approvisionné. Il n’existe aucune limite sur le débit approvisionné maximal que vous pouvez configurer sur la base de données ou le conteneur. Le débit approvisionné minimal dépend des facteurs suivants : 

* La taille maximale des données que vous stockez jamais dans le conteneur
* Le débit maximal que vous devez configurer jamais sur le conteneur
* Le nombre maximal de conteneurs Azure Cosmos que vous créez jamais dans une base de données avec un débit partagé. 

Vous pouvez récupérer le débit minimal d’un conteneur ou d’une base de données par programmation à l’aide de kits de développement logiciel ou affichez la valeur dans le portail Azure. Lors de l’utilisation du SDK .NET, le [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) méthode vous permet de mettre à l’échelle de la valeur de débit approvisionné. Lorsque vous utilisez le SDK Java, le [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) méthode vous permet de mettre à l’échelle de la valeur de débit approvisionné. 

Lors de l’utilisation du SDK .NET, le [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) méthode vous permet de récupérer le débit minimal d’un conteneur ou d’une base de données. 

Vous pouvez faire évoluer le débit approvisionné d’un conteneur ou d’une base de données à tout moment. Vous pouvez exécuter l’opération de mise à l’échelle vers le bas après la période d’inactivité de 4 heures. La période d’inactivité est définie comme le temps période lorsqu’il n’y avait aucune offre des opérations de remplacement (qui inclut la montée et descente en puissance) dans un conteneur ou d’une base de données. 

## <a name="comparison-of-models"></a>Comparaison des modèles

|**Quota**  |**Débit provisionné sur une base de données**  |**Débit provisionné sur un conteneur**|
|---------|---------|---------|
|Unités de requête minimales |400 (Après les quatre premiers conteneurs, chaque conteneur supplémentaire requiert un minimum de 100 RU par seconde.) |400|
|Unités de requête minimales par conteneur|100|400|
|Unités de requête minimales requises pour consommer 1 Go de stockage|40|40|
|Unités de requête maximales|Illimitées, sur la base de données.|Illimitées, sur le conteneur.|
|Unités de requête allouées ou disponibles sur un conteneur spécifique|Aucune garantie. Les unités de requête allouées à un conteneur donné dépendent des propriétés. Les propriétés peuvent être le choix de clés de partition des conteneurs qui partagent le débit, la distribution de la charge de travail et le nombre de conteneurs. |Toutes les unités de requête configurées sur le conteneur sont exclusivement réservées à ce conteneur.|
|Stockage maximal pour un conteneur|Illimité.|Illimité.|
|Débit maximal par partition logique d’un conteneur|10 000 RU/s|10 000 RU/s|
|Débit maximal (données + index) par partition logique d’un conteneur|10 Go|10 Go|

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

