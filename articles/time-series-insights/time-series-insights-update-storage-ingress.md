---
title: Entrée et stockage des données dans Azure Time Series Insights et préversion | Microsoft Docs
description: Présentation de l’entrée et du stockage des données dans Azure Time Series Insights - Préversion.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: a37021d11de86fc0958f330f4f594e25e3aa00bd
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618201"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrée et stockage des données dans Azure Time Series Insights - Préversion

Cet article décrit les modifications apportées à l’entrée et au stockage des données à partir d’Azure Time Series Insights - Préversion. Il couvre la structure de stockage sous-jacente, le format des fichiers et la propriété ID Time Series. L’article aborde également le processus d’entrée sous-jacent, le débit et les limitations.

## <a name="data-ingress"></a>Entrée de données

Les stratégies d’entrée de données Azure Time Series Insights déterminent la provenance possible des données et leur format.

[![Aperçu du modèle de la série chronologique](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Stratégies d’entrée

La préversion de Time Series Insights prend en charge les mêmes sources d’événements et les mêmes types de fichiers que Time Series Insights à l’heure actuelle :

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights prend en charge le format JSON via Azure IoT Hub ou Azure Event Hubs. Pour optimiser vos données JSON IoT, découvrez [Comment mettre en forme JSON](./time-series-insights-send-events.md#json).

### <a name="data-storage"></a>Stockage des données

Lorsque vous créez un environnement de référence SKU pour un paiement à l’utilisation de Time Series Insights - Préversion, vous créez deux ressources :

* Un environnement Time Series Insights.
* Un compte de Stockage Azure universel V1 où les données seront stockées.

La préversion de Time Series Insights utilise le stockage blob Azure avec le type de fichier Parquet. Time Series Insights gère toutes les opérations de données, y compris la création d’objets blob, l’indexation et le partitionnement des données dans le compte de Stockage Azure. Vous créez ces objets blob à l’aide d’un compte de Stockage Azure.

Comme les autres objets blob de Stockage Azure, les objets blob créés par Time Series Insights vous permettent de lire et d’écrire ces objets blob afin de prendre en charge plusieurs scénarios d’intégration.

### <a name="data-availability"></a>Disponibilité des données

La préversion de Time Series Insights indexe les données à l’aide d’une stratégie d’optimisation de la taille des objets blob. Les données deviennent disponibles pour les requêtes après l’indexation, ce qui repose sur la quantité de données entrante et la vélocité d’entrée des données.

> [!IMPORTANT]
> * La version de disponibilité générale (GA) Time Series Insights rend les données disponibles dans les 60 secondes après l’arrivée à une source d’événement.
> * Avec la préversion, prévoyez plus de temps pour la mise à disponibilité des données.
> * Si vous subissez une latence importante, contactez-nous.

### <a name="scale"></a>Scale

La préversion de Time Series Insights prend en charge une mise à l’échelle de l’entrée initiale allant jusqu’à 1 méga-octets par seconde (Mbit/s) et par environnement. L’amélioration de la prise en charge de la mise à l’échelle est en cours. Nous prévoyons de mettre à jour notre documentation pour refléter ces améliorations.

## <a name="parquet-file-format"></a>Format de fichier Parquet

Le format Parquet est un format de fichier de données en colonnes, conçu pour :

* Interopérabilité
* Optimiser l’espace
* Optimiser les requêtes

Time Series Insights a choisi le format Parquet, car il offre des schémas efficaces de compression et de codage des données, ainsi que de meilleures performances permettant de gérer en bloc les données complexes.

Pour plus d’informations sur le type de fichier Parquet, consultez la [documentation de Parquet](https://parquet.apache.org/documentation/latest/).

Pour plus d’informations sur le format de fichier Parquet dans Azure, consultez [Types de fichiers pris en charge dans Stockage Azure](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Structure d’événement dans Parquet

Time Series Insights crée et stocke des copies d’objets blob dans les deux formats suivants :

1. Le premier, copie initiale, est partitionné en fonction de l’heure d’arrivée :

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Heure de création d’objets blob pour les objets blob partitionnés par heure d’arrivée.

1. Le second, copie repartitionnée, est partitionné par un regroupement dynamique d’ID Time Series :

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Timestamp d’événement minimal dans un objet blob pour les objets blob partitionnés par l’ID Time Series.

> [!NOTE]
> * `<YYYY>` correspond à une représentation de l’année à 4 chiffres.
> * `<MM>` correspond à une représentation du mois à 2 chiffres.
> * `<YYYYMMDDHHMMSSfff>` correspond à une représentation sous forme de timestamp avec une année à 4 chiffres (`YYYY`), un mois à 2 chiffres (`MM`), un jour à 2 chiffres (`DD`), une heure à 2 chiffres (`HH`), des minutes à 2 chiffres (`MM`), des secondes à 2 chiffres (`SS`) et des millisecondes à 3 chiffres (`fff`).

Les événements Time Series Insights sont mappés au contenu du fichier Parquet comme suit :

* Chaque événement correspond à une ligne unique.
* Colonne **Timestamp** intégrée présentant un timestamp d’événement. La propriété Timestamp n’est jamais nulle. Par défaut, elle a la valeur **Event Source Enqueued Time** si la propriété Timestamp n’est pas spécifiée dans l’événement source. Le timestamp est au format UTC. 
* Toutes les autres propriétés mappées aux colonnes se terminent par `_string` (chaîne), `_bool` (booléen), `_datetime` (DateHeure) et `_double` (double), selon le type de propriété.
* Cela correspond au schéma de mappage pour la première version du format de fichier, que nous appelons **V=1**. Avec l’évolution de cette fonctionnalité, le nom est incrémenté : **V=2**, **V=3**, etc.

## <a name="azure-storage"></a>Stockage Azure

Cette section décrit les détails du stockage Azure relatifs à Azure Time Series Insights.

Pour obtenir une description complète du service de stockage d’objets blob Azure, lisez l’[introduction aux objets blob de stockage](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Votre compte de stockage

Lorsque vous créez un environnement de paiement à l’utilisation Time Series Insights, vous créez deux ressources : un environnement Time Series Insights et un compte universel V1 de Stockage Azure où les données seront stockées. Nous avons choisi Stockage Azure universel V1 comme ressource par défaut en raison de son interopérabilité, de son prix et de ses performances.

Time Series Insights publie jusqu’à deux copies de chaque événement dans votre compte de Stockage Azure. La copie initiale est toujours conservée afin que vous puissiez l’interroger rapidement à l’aide d’autres services. Vous pouvez facilement utiliser Spark, Hadoop et d’autres outils familiers dans les ID Time Series sur les fichiers Parquet bruts, car ces moteurs prennent en charge le filtrage du nom de fichier de base. Le regroupement des objets blob par année et par mois est un moyen utile de répertorier les objets blob dans un intervalle de temps spécifique pour un travail personnalisé.

En outre, Time Series Insights repartitionne les fichiers Parquet afin d’optimiser les API Time Series Insights. Le fichier le plus récemment repartitionné est aussi sauvegardé.

Lors de la préversion publique, les données sont stockées indéfiniment dans votre compte de Stockage Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Écriture et modification des objets blob Time Series Insights

Pour garantir les performances des requêtes et la disponibilité des données, ne modifiez pas et ne supprimez pas tous les objets blob qui sont créés par Time Series Insights.

> [!TIP]
> Les performances de Time Series Insights peuvent être compromises si vous lisez ou écrivez trop souvent dans vos objets blob.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accès et exportation des données à partir de Time Series Insights - Préversion

Vous souhaiterez peut-être accéder aux données stockées dans l’explorateur Time Series Insights - Préversion à utiliser conjointement avec d’autres services. Par exemple, vous souhaiterez peut-être utiliser vos données afin de créer un rapport dans Power BI, de procéder à du Machine Learning à l’aide d’Azure Machine Learning Studio, ou d’utiliser une application notebook avec Jupyter Notebook.

Vous pouvez accéder à vos données au moyen de trois méthodes générales :

* À partir de l’explorateur de la préversion de Time Series Insights : vous pouvez exporter des données dans un fichier CSV à partir de l’explorateur de la préversion de Time Series Insights. Pour plus d’informations, consultez [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md) (Explorateur Time Series Insights - Préversion).
* À partir des API de la préversion de Time Series Insights : vous pouvez accéder au point de terminaison de l’API sur `/getRecorded`. Pour en savoir plus sur cette API, consultez [Requête de série chronologique](./time-series-insights-update-tsq.md).
* Directement à partir d’un compte de Stockage Azure (ci-dessous).

#### <a name="from-an-azure-storage-account"></a>À partir d’un compte de Stockage Azure

* Vous avez besoin d’un accès en lecture pour tous les comptes que vous utilisez afin d’accéder à vos données Time Series Insights. Pour en savoir plus, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).
* Pour plus d’informations sur les méthodes directes de lecture des données à partir du stockage d’objets blob Azure, consultez [Choisir une solution Azure pour le transfert de données](../storage/common/storage-choose-data-transfer-solution.md).
* Pour exporter des données à partir d’un compte de Stockage Azure :
    * Tout d’abord, assurez-vous que votre compte répond aux conditions requises pour l’exportation des données. Pour plus d’informations, consultez [Configuration système requise du service Azure Import/Export](../storage/common/storage-import-export-requirements.md).
    * Pour en savoir plus sur les autres méthodes d’exportation des données à partir de votre compte de stockage Azure, consultez [Utilisation du service Azure Import/Export pour exporter des données à partir du Stockage Blob Azure](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Suppression des données

Ne supprimez pas les objets blob. Ils sont utiles pour les audits et pour conserver un enregistrement de vos données. De plus, la préversion de Time Series Insights maintient les métadonnées de blob dans chaque objet blob.

## <a name="partitions"></a>Partitions

Chaque environnement de la préversion de Time Series Insights doit avoir une propriété **Time Series ID** et une propriété **Timestamp** qui l’identifient de façon unique. Votre ID Time Series agit comme une partition logique pour vos données et apporte à l’environnement Time Series Insights - Préversion une frontière naturelle pour la distribution des données entre les partitions physiques. Les partitions physiques sont gérées par la préversion de Time Series Insights dans un compte de Stockage Azure.

Time Series Insights utilise le partitionnement dynamique pour optimiser les performances de stockage et de requête en supprimant et en recréant des partitions. L’algorithme de partitionnement dynamique Time Series Insights - Préversion tente d’empêcher une partition physique unique d’avoir des données sur plusieurs partitions logiques distinctes. En d’autres termes, l’algorithme de partitionnement conserve toutes les données spécifiques d’un ID Time Series unique exclusivement dans les fichiers Parquet sans entrelacement avec d’autres ID Time Series. L’algorithme de partitionnement dynamique tente également de conserver l’ordre d’origine des événements au sein d’un ID Time Series unique.

Initialement, lors de l’entrée des données, ces dernières sont partitionnées en fonction de Timestamp afin qu’une partition logique unique au sein d’un intervalle de temps donné puisse être répartie sur plusieurs partitions physiques. Une partition physique unique peut également contenir de nombreuses partitions logiques, voire la totalité des partitions logiques. En raison des limitations de taille des objets blob, même avec un partitionnement optimal, une partition logique unique peut occuper plusieurs partitions physiques.

> [!NOTE]
> Par défaut, la valeur Timestamp est le message *Enqueued Time* dans votre source d’événement configurée.

Si vous chargez des données historiques ou des messages par lot, assignez la valeur que vous souhaitez stocker avec vos données à la propriété Timestamp qui correspond au timestamp approprié. La propriété Timestamp est sensible à la casse. Pour plus d’informations, consultez [Modèle de série chronologique](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partitions physiques

Une partition physique est un objet blob de blocs qui est stocké dans votre compte de stockage. La taille réelle des objets blob peut varier, car elle dépend du taux d’envoi. Toutefois, nous estimons que la taille des objets blob sera comprise entre 20 Mo et 50 Mo. Cette hypothèse a amené l’équipe Time Series Insights à choisir 20 Mo comme taille optimale des performances de requête. Cette taille peut changer au fil du temps, selon la taille du fichier et la vélocité d’entrée des données.

> [!NOTE]
> * Les objets blob font 20 Mo.
> * Les objets blob Azure sont parfois supprimés et recréés afin d’être repartitionnés pour de meilleures performances.
> * En outre, les mêmes données Time Series Insights peuvent être présentes dans plusieurs objets blob.

### <a name="logical-partitions"></a>Partitions logiques

Une partition logique est une partition située dans une partition physique et qui stocke toutes les données associées à une valeur de clé de partition unique. La préversion de Time Series Insights partitionne chaque objet blob de manière logique selon deux propriétés :

* **Time Series ID** : clé de partition pour toutes les données Time Series Insights dans le flux d’événements et le modèle.
* **Timestamp** : heure, en fonction de l’entrée initiale.

La préversion de Time Series Insights fournit des requêtes performantes basées sur ces deux propriétés. Ces deux propriétés offrent également la méthode la plus efficace pour fournir rapidement des données Time Series Insights.

Il est important de sélectionner un ID Time Series approprié, car il s’agit d’une propriété immuable. Pour plus d’informations, consultez [Choose Time Series IDs](./time-series-insights-update-how-to-id.md) (Sélectionner des ID Time Series).

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Azure Time Series Insights - Préversion - Stockage et entrée](./time-series-insights-update-storage-ingress.md).

- En savoir plus sur la nouvelle [modélisation des données](./time-series-insights-update-tsm.md).
