---
title: Utilisation du support de flux de modification dans Azure Cosmos DB
description: Utiliser le support de flux de modification d'Azure Cosmos DB pour le suivi des modifications des documents, les opérations de traitement basées sur les événements comme les déclencheurs, et la mise à jour des systèmes de cache et d'analyse
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: bf36c0697b5e30c77610d30475be20adc18810cd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445588"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Flux de modification dans Azure Cosmos DB - Vue d'ensemble

Dans Azure Cosmos DB, le flux de modification écoute les modifications apportées à un conteneur Azure Cosmos. Il renvoie ensuite la liste chronologique de documents qui ont été modifiés, dans l’ordre dans lequel ils ont été modifiés. Les modifications sont conservées et peuvent être traitées de manière asynchrone et incrémentielle, puis réparties sur un ou plusieurs consommateurs pour un traitement en parallèle. 

Azure Cosmos DB est particulièrement bien adapté aux applications d’IoT, de jeux, de vente au détail et de journal des opérations. Ces applications intègrent souvent un modèle de conception qui consiste à utiliser des modifications de données pour déclencher des actions supplémentaires. Voici quelques exemples d’actions supplémentaires :

* Déclenchement d’une notification ou d’un appel d’API lorsqu’un élément est inséré ou modifié
* Traitement du flux de données en temps réel pour l’IoT ou traitement d’analyse des données opérationnelles en temps réel
* Déplacement de données supplémentaire via une synchronisation avec un cache, un moteur de recherche ou un entrepôt de données, ou archivage de données dans un stockage à froid

Le flux de modification Azure Cosmos DB vous permet de créer des solutions efficaces et scalables pour chacun de ces modèles, comme illustré dans l’image suivante :

![Utilisation du flux de modification d’Azure Cosmos DB pour alimenter les analyses en temps réel et les scénarios de calcul pilotés par les événements](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>API et SDK clients pris en charge

Cette fonctionnalité est prise en charge par les API et les SDK clients Azure Cosmos DB suivants.

| **Pilotes clients** | **Azure CLI** | **API SQL** | **API Azure Cosmos DB pour Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin**|**API de table** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N/D | Oui | Oui | Oui | Oui | Non |
|Java|N/D|Oui|Oui|Oui|Oui|Non|
|Python|N/D|Oui|Oui|Oui|Oui|Non|
|Node/JS|N/D|Oui|Oui|Oui|Oui|Non|

## <a name="change-feed-and-different-operations"></a>Flux de modification et différentes opérations

Aujourd’hui, vous pouvez voir toutes les opérations dans le flux de modification. Il n’est pas encore possible de contrôler le flux de modification pour certaines opérations (par exemple, mises à jour sans insertions). Vous pouvez ajouter un « marqueur conditionnel » de mise à jour à l’élément, et filtrer les résultats selon ce marqueur lorsque vous effectuez le traitement des éléments du flux de modification. Pour le moment, le flux de modification ne consigne pas les suppressions. Comme pour l’exemple précédent, vous pouvez ajouter un marqueur conditionnel aux éléments en cours de suppression. Par exemple, vous pouvez ajouter l’attribut « supprimé » à l’élément, lui attribuer la valeur « true », puis définir une durée de vie pour l’élément, afin que celui-ci soit supprimé automatiquement. Vous pouvez lire le flux de modification pour les éléments historiques, par exemple, pour les éléments qui ont été ajoutés il y a cinq ans (la modification la plus récente correspondant à l’élément, il n’inclut pas les modifications intermédiaires). Si l’élément n’a pas été supprimé, vous pouvez lire le flux de modification correspondant en remontant jusqu’à la date de création de votre conteneur.

### <a name="sort-order-of-items-in-change-feed"></a>Ordre de tri des éléments du flux de modification

Les éléments du flux de modification figurent dans l’ordre où ils ont été modifiés. Cet ordre de tri est garanti par clé de partition logique.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Flux de modification dans les comptes Azure Cosmos multirégions

Dans un compte Azure Cosmos multirégion, si une région d’écriture bascule, le flux de modification continue de fonctionner après le basculement manuel de manière contiguë.

### <a name="change-feed-and-time-to-live-ttl"></a>Flux de modification et durée de vie (TTL)

Si une propriété de durée de vie (TTL) est définie sur -1 pour un élément, le flux de modification est conservé indéfiniment. Tant que les données ne sont pas supprimées, elles restent dans le flux de modification.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Flux de modification et formats _etag, _lsn ou _ts

Le format _etag est un format interne. Vous ne devez pas vous y fier, car il peut être modifié à tout moment. Le format _ts correspond à un horodatage de modification ou de création. Vous pouvez utiliser les données _ts à des fins de comparaison chronologique. Le format _lsn est un ID de lot qui est ajouté uniquement pour le flux de modification. Il représente l’ID de transaction. De nombreux éléments peuvent avoir un même _lsn. L’étiquette d’entité (ETag) de FeedResponse est différente du _etag que vous voyez dans l’élément. _etag est un identificateur interne utilisé pour le contrôle de l’accès concurrentiel, qui indique la version du document, alors que l’ETag est utilisé pour le séquencement du flux.

## <a name="change-feed-use-cases-and-scenarios"></a>Cas d’utilisation et scénarios du flux de modification

Le flux de modification permet de traiter efficacement les grands jeux de données avec un volume important d’écritures. Le flux de modification offre également une alternative à l’interrogation d’un jeu de données complet dans le but d’y identifier les changements.

### <a name="use-cases"></a>Cas d'utilisation

Par exemple, avec un flux de modification, vous pouvez effectuer efficacement les tâches suivantes :

* Mettre à jour un cache, mettre à jour l’index de recherche ou mettre à jour un entrepôt de données avec les données stockées dans Azure Cosmos DB

* Implémenter la hiérarchisation et l’archivage des données de niveau application. Par exemple, stocker les « données chaudes » dans Azure Cosmos DB et archiver les « données froides » dans d’autres systèmes de stockage, tels que le [Stockage Blob Azure](../storage/common/storage-introduction.md)

* Effectuer des migrations sans aucun temps d’arrêt vers un autre compte ou un autre conteneur Azure Cosmos avec une clé de partition logique différente

* Implémenter une [architecture lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) à l’aide d’Azure Cosmos DB, qui prend en charge à la fois les couches de service de requêtes en temps réel et par lots, permettant ainsi une architecture lambda avec un faible coût total de possession

* Recevoir et stocker des données d’événement à partir d’appareils, de capteurs, d’infrastructures et d’applications, et traiter ces événements en temps réel, à l’aide de [Spark](../hdinsight/spark/apache-spark-overview.md), par exemple  L’illustration suivante montre comment implémenter l’architecture lambda à l’aide d’Azure Cosmos DB via le flux de modification :

![Pipeline lambda Azure Cosmos DB pour l’ingestion et l’interrogation](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scénarios

Voici quelques-uns des scénarios que vous pouvez facilement implémenter avec le flux de modification :

* Dans les applications web et mobiles [serverless](https://azure.microsoft.com/solutions/serverless/), vous pouvez suivre les événements tels que les modifications apportées au profil, aux préférences ou à l’emplacement de votre client, ou déclencher certaines actions, comme l’envoi de notifications Push à ses appareils avec [Azure Functions](change-feed-functions.md).

* Si vous utilisez Azure Cosmos DB pour créer un jeu, vous pouvez, par exemple, utiliser le flux de modification pour implémenter des classements en temps réel basés sur des scores de jeux terminés.


## <a name="working-with-change-feed"></a>Utilisation du flux de modification

Vous pouvez utiliser le flux de modification à l’aide des options suivantes :

* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
* [Utilisation du flux de modification avec le processeur de flux de modification](change-feed-processor.md) 

Le flux de modification est disponible pour chacune des clés de partition logique du conteneur, et il peut être distribué vers un ou plusieurs consommateurs en vue d’un traitement parallèle, comme indiqué dans l’image ci-dessous.

![Traitement distribué du flux de modification d’Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Fonctionnalités du flux de modification

* Le flux de modification est activé par défaut pour tous les comptes Azure Cosmos.

* Vous pouvez utiliser votre [débit provisionné](request-units.md) pour lire le contenu du flux de modification, comme toute autre opération effectuée dans Azure Cosmos DB, dans l’une des régions associées à votre base de données Azure Cosmos.

* Le flux de modification inclut les opérations d’insertion et de mise à jour apportées aux éléments du conteneur. Vous pouvez capturer des suppressions en définissant un indicateur de « suppression réversible » dans vos éléments (par exemple, vos documents) au lieu d’effectuer des suppressions définitives. Vous pouvez également définir un délai d’expiration fixe pour vos éléments avec la [fonctionnalité de durée de vie](time-to-live.md). Par exemple, vous pouvez définir le délai sur 24 heures et utiliser la valeur de cette propriété pour capturer les suppressions. Avec cette solution, vous devez traiter les modifications dans un intervalle de temps inférieur à la période d’expiration de durée de vie. 

* Chaque modification apportée à un élément n’apparaît qu’une seule fois dans le flux de modification. En outre, les clients doivent gérer leur logique de points de contrôle. Si vous souhaitez éviter la gestion complexe des points de contrôle, le processeur du flux de modification fournit des points de contrôle automatiques ainsi qu’une sémantique de type « au moins une fois ». Consultez [Utilisation du flux de modification avec le processeur de flux de modification](change-feed-processor.md).

* Seule la dernière modification d’un élément donné est incluse dans le journal des modifications. Les modifications intermédiaires peuvent ne pas être disponibles.

* Le flux de modification est trié par ordre de modification au sein de chaque valeur de clé de partition logique. Il n’existe aucun ordre garanti entre les valeurs de clé de partition.

* Les modifications peuvent être synchronisées à partir de n’importe quel point dans le temps. Autrement dit, il n’existe aucune période fixe de conservation des données pendant laquelle les modifications sont disponibles.

* Les modifications sont disponibles en parallèle pour toutes les clés de partition logique d’un conteneur Azure Cosmos. Cette fonctionnalité permet à plusieurs consommateurs de traiter en parallèle les modifications de grands conteneurs.

* Les applications peuvent demander plusieurs flux de modification simultanément pour un même conteneur. ChangeFeedOptions.StartTime peut être utilisé pour fournir un point de départ. Par exemple, pour rechercher le jeton de continuation correspondant à une heure donnée. S’il est spécifié, ContinuationToken l’emporte sur les valeurs StartTime et StartFromBeginning. La précision de ChangeFeedOptions.StartTime est ’environ 5 secondes. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Flux de modification dans les API pour Cassandra et MongoDB

La fonctionnalité de flux de modification apparaît comme un flux de modification dans l’API MongoDB et sous la forme Interroger avec un prédicat dans l’API Cassandra. Pour en savoir plus sur les détails d’implémentation de l’API MongoDB, consultez les [flux de modification dans l’API Azure Cosmos DB pour MongoDB](mongodb-change-streams.md).

Native Apache Cassandra propose la capture des changements de données (CDC), un mécanisme de marquage de tables spécifiques à des fins d’archivage, mais aussi de rejet des écritures dans ces tables une fois que la taille sur disque configurable du journal CDC est atteinte. La fonctionnalité de flux de modification dans l’API Azure Cosmos DB pour Cassandra améliore la capacité à interroger les modifications avec un prédicat par le biais du langage CQL. Pour en savoir plus sur les détails d’implémentation, consultez [Flux de modification dans l’API Azure Cosmos DB pour Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Options de lecture du flux de modification](read-change-feed.md)
* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
* [Utilisation du processeur de flux de modification](change-feed-processor.md)
