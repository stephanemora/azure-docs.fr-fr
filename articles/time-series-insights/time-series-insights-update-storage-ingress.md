---
title: Entrée et stockage des données dans la préversion – Azure Time Series Insights | Microsoft Docs
description: Découvrez l’entrée et le stockage des données dans Azure Time Series Insights (préversion).
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: dada1a8ed8b1725905ee2ad159e385d1bee62fc6
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615093"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrée et stockage des données dans Azure Time Series Insights - Préversion

Cet article décrit les mises à jour de l’entrée et du stockage des données pour Azure Time Series Insights (préversion). Il couvre la structure de stockage sous-jacente, le format des fichiers et la propriété ID Time Series. Il aborde également le processus d’entrée sous-jacent, les meilleures pratiques et les limitations actuelles de la préversion.

## <a name="data-ingress"></a>Entrée de données

Votre environnement Azure Time Series Insights contient un moteur d’ingestion pour collecter, traiter et stocker des données de série chronologique. Lors de la planification de votre environnement, il est important de tenir compte de certaines considérations afin de garantir que toutes les données entrantes sont traitées, d’obtenir une grande échelle d’entrée et de réduire la latence d’ingestion (le temps pris par TSI pour lire et traiter les données de l’événement source). 

Dans Time Series Insights (préversion), les stratégies d’entrée de données déterminent la provenance possible des données et leur format.

### <a name="ingress-policies"></a>Stratégies d’entrée

Time Series Insights (préversion) prend en charge les sources d’événements suivantes :

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights (préversion) prend en charge un maximum de deux sources d’événements par instance. Azure Time Series Insights prend en charge le format JSON via Azure IoT Hub ou Azure Event Hubs.

> [!WARNING] 
> * Vous pouvez rencontrer une latence initiale élevée lors de la jonction d’une source d’événement à votre environnement en préversion. 
> La latence de la source d’événement dépend du nombre d’événements actuellement présents dans votre IoT Hub ou votre Event Hub.
> * Une latence élevée sera impartie après la réception des données de la source d’événements. Veuillez nous contacter en soumettant un ticket de support via le Portail Azure si vous rencontrez une latence élevée de façon continue.

## <a name="ingress-best-practices"></a>Meilleures pratiques relatives à l’entrée

Nous vous recommandons d’utiliser les meilleures pratiques suivantes :

* Configurez Time Series Insights et un IoT Hub ou Event Hub dans la même région. Cela réduira la latence d’ingestion induite par le réseau.
* Planifiez vos besoins en matière de mise à l’échelle en calculant le taux d’ingestion escompté et en vérifiant qu’il se situe en deçà du taux pris en charge indiqué ci-dessous.
* Découvrez comment optimiser et façonner vos données JSON, ainsi que les limitations actuelles de la préversion, en lisant [comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Mise à l’échelle de l’entrée et limitations de la préversion

Par défaut, les environnements en préversion préliminaire prennent en charge des débits d’entrée allant jusqu’à **1 mégaoctet par seconde (Mo/s) par environnement**. Les clients peuvent augmenter le débit de leurs environnements en préversion jusqu’à **16 Mo/s** si nécessaire.
Une limite de **0,5 Mo/s** par partition est également fixée. 

La limite par partition n’est pas sans conséquence pour les clients qui utilisent IoT Hub. Spécifiquement, en raison de l’affinité entre un appareil IoT Hub et une partition. Dans les scénarios où un appareil de passerelle transfère des messages au hub en utilisant son propre ID d’appareil et sa propre chaîne de connexion, il existe un risque d’atteindre la limite de 0,5 Mo/s, étant donné que les messages arrivent dans une partition unique, même si la charge utile de l’événement spécifie des ID de série chronologique différents. 

En général, les taux d’entrée sont considérés comme le facteur du nombre d’appareils qui se trouvent dans votre organisation, de la fréquence d’émission des événements et de la taille de chaque événement :

*  **Nombre d’appareils** × **Fréquence d’émission d’événements** × **Taille de chaque événement**.

> [!TIP]
> Pour les environnements qui utilisent IoT Hub en tant que source d’événement, calculez le taux d’ingestion en utilisant le nombre de connexions au hub en cours d’utilisation, plutôt que le nombre total d’appareils en cours d’utilisation ou dans l’organisation.

Pour plus d’informations sur les unités de débit, les limites et les partitions :

* [Mise à l’échelle d’IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Mise à l’échelle d’Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partitions Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Stockage des données

Lorsque vous créez un environnement de référence SKU pour un paiement à l’utilisation Time Series Insights (préversion), vous créez deux ressources Azure :

* Un environnement Time Series Insights (préversion) qui peut éventuellement inclure des fonctionnalités de stockage chaud.
* Un compte d’objets blob GPv1 Stockage Azure pour le stockage froid de données.

Les données de votre magasin chaud sont disponibles uniquement via [Time Series Query](./time-series-insights-update-tsq.md) et l’[explorateur d’Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md). 

Time Series Insights (préversion) enregistre les données de votre magasin froid dans le stockage Blob Azure au [format de fichier Parquet](#parquet-file-format-and-folder-structure). Time Series Insights (préversion) gère ces données de magasin froid en exclusivité, mais vous pouvez les lire directement en tant que fichiers Parquet standard.

> [!WARNING]
> En tant que propriétaire du compte de stockage Blob Azure où résident les données du magasin froid, vous avez un accès total à toutes les données du compte. Cet accès comprend des autorisations d’écriture et de suppression. Ne modifiez pas ni ne supprimez les données que Time Series Insights (préversion) écrit, car cela peut entraîner une perte de données.

### <a name="data-availability"></a>Disponibilité des données

Time Series Insights (préversion) partitionne et indexe les données pour optimiser les performances des requêtes. Les données deviennent disponibles pour la requête une fois qu’elles sont indexées. La quantité de données en cours d’ingestion peut avoir une incidence sur cette disponibilité.

> [!IMPORTANT]
> La version de disponibilité générale (GA) à venir de Time Series Insights rend les données disponibles dans les 60 secondes suivant leur lecture à partir de la source de l’événement. Pendant la préversion, vous pouvez constater une période plus longue avant que les données ne soient disponibles. Si vous constatez une latence significative supérieure à 60 secondes, veuillez envoyer un ticket de support par le biais du Portail Azure.

## <a name="azure-storage"></a>Stockage Azure

Cette section décrit les détails du stockage Azure relatifs à Azure Time Series Insights (préversion).

Pour obtenir une description complète du stockage Blob Azure, lisez l’[introduction aux objets blob de stockage](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Votre compte de stockage

Lorsque vous créez un environnement Time Series Insights (préversion) pour un paiement à l’utilisation, un compte d’objets blob GPv1 Stockage Azure est créé en tant que magasin froid à long terme.  

Time Series Insights (préversion) publie jusqu’à deux copies de chaque événement dans votre compte de Stockage Azure. La copie initiale possède des événements classés par heure d’ingestion et est toujours conservée, ce qui vous permet d’utiliser d’autres services pour y accéder. Vous pouvez utiliser Spark, Hadoop et d’autres outils familiers pour traiter les fichiers Parquet bruts. 

Time Series Insights (préversion) repartitionne les fichiers Parquet afin d’optimiser la requête Time Series Insights. Cette copie repartitionnée des données est également enregistrée.

Lors de la préversion publique, les données sont stockées indéfiniment dans votre compte de Stockage Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Écriture et modification des objets blob Time Series Insights

Pour garantir les performances des requêtes et la disponibilité des données, ne modifiez ni ne supprimez aucun objet blob créé par Time Series Insights (préversion).

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accès et exportation des données à partir de Time Series Insights - Préversion

Vous souhaiterez peut-être accéder aux données affichées dans l’explorateur de Time Series Insights (préversion) pour les utiliser conjointement avec d’autres services. Par exemple, vous pouvez utiliser vos données pour générer un rapport dans Power BI ou pour effectuer l’apprentissage d’un modèle Machine Learning à l’aide d’Azure Machine Learning Studio. Vous pouvez également utiliser vos données pour transformer, visualiser et modéliser vos Jupyter Notebooks.

Vous pouvez accéder à vos données au moyen de trois méthodes générales :

* À partir de l’explorateur Time Series Insights - Préversion. Vous pouvez exporter des données en tant que fichier CSV à partir de l’explorateur. Pour plus d’informations, consultez [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md) (Explorateur Time Series Insights - Préversion).
* À partir de l’API Time Series Insights (préversion). Vous pouvez atteindre le point de terminaison de l’API à `/getRecorded`. Pour en savoir plus sur cette API, consultez [Requête de série chronologique](./time-series-insights-update-tsq.md).
* Directement à partir d’un compte de Stockage Azure. Vous avez besoin d’un accès en lecture pour tous les comptes que vous utilisez afin d’accéder à vos données Time Series Insights (préversion). Pour en savoir plus, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Suppression des données

Ne supprimez pas vos fichiers Time Series Insights (préversion). Gérez les données associées à partir de Time Series Insights (préversion) uniquement.

## <a name="parquet-file-format-and-folder-structure"></a>Format de fichier Parquet et structure de dossiers

Parquet est un format de fichier en colonnes open source qui a été conçu pour un stockage et des performances efficaces. Time Series Insights (préversion) utilise le format Parquet pour ces raisons. Il partitionne les données par ID Time Series pour des performances de requête à l’échelle.  

Pour plus d’informations sur le type de fichier Parquet, consultez la [documentation relative au format Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights (préversion) stocke des copies de vos données comme suit :

* La première copie initiale est partitionnée par heure d’ingestion et stocke les données par ordre approximatif d’arrivée. Les données se trouvent dans le dossier `PT=Time` :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La deuxième copie repartitionnée est partitionnée par un regroupement d’ID Time Series et se trouve dans le dossier `PT=TsId` :

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Dans les deux cas, les valeurs de temps correspondent à l’heure de création du blob. Les données du dossier `PT=Time` sont conservées. Les données du dossier `PT=TsId` seront optimisées pour la requête dans le temps et ne resteront pas statiques.

> [!NOTE]
> * `<YYYY>` correspond à une représentation de l’année à quatre chiffres.
> * `<MM>` correspond à une représentation du mois à deux chiffres.
> * `<YYYYMMDDHHMMSSfff>` correspond à une représentation de l’horodatage avec une année à quatre chiffres (`YYYY`), un mois à deux chiffres (`MM`), un jour à deux chiffres (`DD`), une heure à deux chiffres (`HH`), une minute à deux chiffres (`MM`), une seconde à deux chiffres (`SS`) et une milliseconde à trois chiffres (`fff`).

Les événements Time Series Insights (préversion) sont mappés au contenu du fichier Parquet comme suit :

* Chaque événement correspond à une ligne unique.
* Chaque ligne comprend la colonne **timestamp** avec un horodatage de l’événement. La propriété timestamp n’est jamais nulle. Par défaut, elle a la valeur **event enqueued time** si la propriété timestamp n’est pas spécifiée dans la source de l’événement. L’horodatage est toujours au format UTC.
* Chaque ligne comprend la colonne ID Time Series telle qu’elle est définie lors de la création de l’environnement Time Series Insights. Le nom de la propriété comprend le suffixe `_string`.
* Toutes les autres propriétés envoyées en tant que données de télémétrie sont mappées à des noms de colonnes qui se terminent par `_string` (chaîne), `_bool` (booléen), `_datetime` (Date/Heure) et `_double` (double), selon le type de propriété.
* Ce schéma de mappage s’applique à la première version du format de fichier, référencée comme **V=1**. Avec l’évolution de cette fonctionnalité, le nom est susceptible d’être incrémenté.

## <a name="next-steps"></a>Étapes suivantes

- Lire [Azure Time Series Insights (préversion) – Stockage et entrée](./time-series-insights-update-storage-ingress.md).

- En savoir plus sur la nouvelle [modélisation des données](./time-series-insights-update-tsm.md).
