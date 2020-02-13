---
title: Utiliser l’ingestion de streaming pour ingérer des données dans Azure Data Explorer
description: Découvrez comment ingérer (charger) des données dans Azure Data Explorer en utilisant l’ingestion de streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cc152460be777c30d79f783b9acfa846a4c73a72
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188019"
---
# <a name="streaming-ingestion-preview"></a>Ingestion de streaming (préversion)

L’ingestion de streaming cible les scénarios qui nécessitent une faible latence avec une durée d’ingestion de moins de 10 secondes pour des volumes de données variés. Elle est utilisée pour optimiser le traitement opérationnel de nombreuses tables, dans une ou plusieurs bases de données où le flux de données dans chaque table est relativement faible (peu d’enregistrements par seconde), mais le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde).

Utilisez l’ingestion classique (en bloc) à la place de l’ingestion de streaming lorsque la quantité de données dépasse 1 Mo par seconde par table. Consultez [Vue d’ensemble de l’ingestion de données](/azure/data-explorer/ingest-data-overview) pour en apprendre plus sur les différentes méthodes d’ingestion.

> [!NOTE]
> L’ingestion de streaming ne prend pas en charge les fonctionnalités suivantes :
> * [Curseurs de base de données](/azure/kusto/management/databasecursor).
> * [Mappage des données](/azure/kusto/management/mappings). Seul un mappage de données [précréé](/azure/kusto/management/create-ingestion-mapping-command) est pris en charge. 

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous à l’[interface utilisateur web](https://dataexplorer.azure.com/).
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Activer l’ingestion de streaming sur votre cluster

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.
 
    ![Ingestion de streaming activée](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Dans l’[interface utilisateur web](https://dataexplorer.azure.com/), définissez la [stratégie d’ingestion de streaming](/azure/kusto/concepts/streamingingestionpolicy) sur les tables et bases de données qui recevront les données de streaming. 

    > [!NOTE]
    > * Si la stratégie est définie au niveau de la base de données, toutes les tables de la base de données sont activées pour l’ingestion de streaming.
    > * La stratégie appliquée ne peut faire référence qu’à des données nouvellement ingérées, et non à d’autres tables de la base de données.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Utiliser l’ingestion de streaming pour ingérer des données dans votre cluster

Il existe deux types d’ingestion de streaming pris en charge :

* [Hub d’événements](/azure/data-explorer/ingest-data-event-hub) utilisé comme source de données
* Une ingestion personnalisée exige que vous écriviez une application qui utilise l’une des bibliothèques clientes Azure Data Explorer. Consultez un [exemple d’ingestion de streaming](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) pour voir un exemple d’application.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Choisir le type d’ingestion de streaming approprié

|   |Event Hub  |Ingestion personnalisée  |
|---------|---------|---------|
|Délai de données entre le lancement de l’ingestion et le moment où les données sont disponibles pour une requête   |    délai plus long     |   délai plus court      |
|Surcharge de développement    |   installation rapide et facile, aucune surcharge de développement    |   surcharge de développement élevée pour l’application afin de gérer les erreurs et d’assurer la cohérence des données     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Désactiver l’ingestion de streaming sur votre cluster

> [!WARNING]
> La désactivation de l’ingestion de streaming peut prendre plusieurs heures.

1. Supprimez la [stratégie d’ingestion de streaming](/azure/kusto/concepts/streamingingestionpolicy) de toutes les tables et bases de données appropriées. La suppression de la stratégie d’ingestion de streaming déclenche le déplacement des données d’ingestion de streaming du stockage initial vers le stockage permanent dans le stockage de colonnes (étendues ou partitions). Le déplacement des données peut durer de quelques secondes à quelques heures, en fonction de la quantité de données dans le stockage initial et de l’utilisation de l’UC et de la mémoire du cluster.
1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

    ![Ingestion de streaming désactivée](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limites

* La capacité et les performances d’ingestion de streaming évoluent avec l’augmentation des tailles de cluster et de machine virtuelle. Les ingestions simultanées sont limitées à 6 ingestions par cœur. Par exemple, pour les SKU 16 cœurs, telles que D14 et L16, la charge maximale prise en charge est de 96 ingestions simultanées. Pour les SKU 2 cœurs, telles que D11, la charge maximale prise en charge est de 12 ingestions simultanées.
* La taille limite des données par demande d’ingestion est de 4 Mo.
* Les mises à jour de schéma, telles que la création et la modification de tables et de mappages d’ingestion, peuvent prendre jusqu’à 5 minutes pour le service d’ingestion de streaming.
* L’activation de l’ingestion de streaming sur un cluster, même lorsque les données ne sont pas ingérées via streaming, utilise une partie du disque SSD local des machines du cluster pour les données d’ingestion de streaming et réduit le stockage disponible pour le cache à chaud.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
