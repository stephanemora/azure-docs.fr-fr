---
title: Entrée et stockage des données dans la préversion – Azure Time Series Insights | Microsoft Docs
description: Découvrez l’entrée et le stockage des données dans Azure Time Series Insights (préversion).
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714291"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrée et stockage des données dans Azure Time Series Insights - Préversion

Cet article décrit les mises à jour de l’entrée et du stockage des données pour Azure Time Series Insights (préversion). Il couvre la structure de stockage sous-jacente, le format des fichiers et la propriété ID Time Series. Il aborde également le processus d’entrée sous-jacent, les meilleures pratiques et les limitations actuelles de la préversion.

## <a name="data-ingress"></a>Entrée de données

Votre environnement Azure Time Series Insights contient un moteur d’ingestion pour collecter, traiter et stocker des données de série chronologique. Lors de la planification de votre environnement, il est important de tenir compte de certaines considérations afin de garantir que toutes les données entrantes sont traitées, d’obtenir une grande échelle d’entrée et de réduire la latence d’ingestion (le temps pris par TSI pour lire et traiter les données de l’événement source). 

Dans Time Series Insights (préversion), les stratégies d’entrée de données déterminent la provenance possible des données et leur format.

### <a name="ingress-policies"></a>Stratégies d’entrée

#### <a name="event-sources"></a>Sources de l’événement

Time Series Insights (préversion) prend en charge les sources d’événements suivantes :

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights (préversion) prend en charge un maximum de deux sources d’événements par instance.

> [!WARNING] 
> * Vous pouvez rencontrer une latence initiale élevée lors de la jonction d’une source d’événement à votre environnement en préversion. 
> La latence de la source d’événement dépend du nombre d’événements actuellement présents dans votre IoT Hub ou votre Event Hub.
> * Une latence élevée sera impartie après la réception des données de la source d’événements. Contactez-nous en soumettant un ticket de support via le Portail Azure si vous rencontrez une latence élevée de façon continue.

#### <a name="supported-data-format-and-types"></a>Formats et types de données pris en charge

Azure Time Series Insights prend en charge le format JSON encodé en UTF8 via IoT Hub ou Azure Event Hubs. 

Voici la liste des types de données pris en charge.

| Type de données | Description |
|-----------|------------------|-------------|
| bool      |   Type de données ayant l’un des deux états suivants : true ou false.       |
| dateTime    |   Représente un instant, généralement exprimé sous la forme d’une date ou d’une heure. Les valeurs DateTime doivent être au format ISO 8601.      |
| double    |   Virgule flottante IEEE 754 64 bits à double précision
| string    |   Valeurs de texte composées de caractères Unicode.          |

#### <a name="objects-and-arrays"></a>Objets et tableaux

Vous pouvez envoyer des types complexes tels que des objets et des tableaux dans le cadre de votre charge utile d’événement, mais vos données subissent un processus d’aplatissement lorsqu’elles sont stockées. Pour plus d’informations sur la façon de mettre en forme vos événements JSON, ainsi que sur le type complexe et l’aplatissement des objets imbriqués, consultez la page [Comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).


### <a name="ingress-best-practices"></a>Meilleures pratiques relatives à l’entrée

Nous vous recommandons d’utiliser les meilleures pratiques suivantes :

* Configurez Time Series Insights et votre IoT Hub ou Event Hub dans la même région afin de réduire la latence d’ingestion du réseau.
* Planifiez vos besoins en matière de mise à l’échelle en calculant le taux d’ingestion escompté et en vérifiant qu’il se situe en deçà du taux pris en charge indiqué ci-dessous.
* Découvrez comment optimiser et façonner vos données JSON, ainsi que les limitations actuelles de la préversion, en lisant [comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Mise à l’échelle de l’entrée et limitations de la préversion

#### <a name="per-environment-limitations"></a>Limitations par environnement

En général, les taux d’entrée sont considérés comme le facteur du nombre d’appareils qui se trouvent dans votre organisation, de la fréquence d’émission des événements et de la taille de chaque événement :

*  **Nombre d’appareils** × **Fréquence d’émission d’événements** × **Taille de chaque événement**.

Par défaut, Time Series Insights (préversion) peut ingérer des données entrantes à un débit allant jusqu’à 1 mégaoctet par seconde (Mbits/s) **par environnement TSI**. Si cela ne répond pas à vos besoins, contactez-nous en soumettant un ticket de support dans le Portail Azure : nous pouvons prendre en charge jusqu’à 16 Mbits/s pour un environnement.
 
Exemple 1 : Contoso Shipping est doté de 100 000 appareils qui émettent un événement trois fois par minute. La taille d’un événement est de 200 octets. Ils utilisent un Event Hub muni de 4 partitions comme source d’événement TSI.
Le taux d’ingestion pour leur environnement TSI est le suivant : 100 000 appareils * 200 octets/événement * (3/60 événements/s) = 1 Mbit/s.
Le taux d’ingestion par partition est de 0,25 Mbits/s.
Le taux d’ingestion de Contoso Shipping se situe dans la limite de l’échelle de la préversion.
 
Exemple 2 : Contoso Fleet Analytics est doté de 60 000 appareils qui émettent un événement chaque seconde. Ils utilisent un IoT Hub muni de 4 partitions comme source d’événement TSI. La taille d’un événement est de 200 octets.
Le taux d’ingestion de l’environnement est le suivant : 20 000 appareils * 200 octets/événement * 1 événements/s = 4 Mbit/s.
Le taux par partition est de 1 Mbits/s.
Contoso Fleet Analytics doit soumettre une demande à TSI par le biais du Portail Azure pour un environnement dédié afin d’atteindre cette échelle.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partitions de hub et limites par partition

Lors de la planification de votre environnement TSI, il est important de tenir compte de la configuration des sources d’événements que vous connecterez à TSI. IoT Hub et Event Hubs utilisent des partitions afin d’activer la mise à l’échelle horizontale pour le traitement des événements.  Une partition est une séquence ordonnée d’événements qui est conservée dans un hub. Le nombre de partitions est défini lors de la phase de création du hub IoT ou d’Event Hubs et n’est pas modifiable. Pour plus d’informations sur la détermination du nombre de partitions, consultez la question « De combien de partitions ai-je besoin ? » sur le FAQ d’Event Hubs. Pour les environnements TSI utilisant IoT Hub, la plupart des hubs IoT ont généralement uniquement besoin de 4 partitions. Que vous soyez en train de créer ou non un hub pour votre environnement TSI ou que vous en utilisiez un existant, vous devez calculer votre taux d’ingestion par partition pour déterminer s’il se trouve dans les limites de la préversion. La préversion de TSI a actuellement une limite **par partition** de 0,5 Mo/s. Utilisez les exemples ci-dessous à titre de référence, et notez les considérations suivantes spécifiques à IoT Hub si vous êtes un utilisateur d’IoT Hub.

#### <a name="iot-hub-specific-considerations"></a>Considérations spécifiques à IoT Hub

Lorsqu’un appareil est créé dans IoT Hub, il est attribué à une partition et l’attribution de la partition n’est pas modifiable. En procédant ainsi, IoT Hub est en mesure de garantir l’ordre des événements. Toutefois, cela a des implications pour TSI en tant que lecteur en aval dans certains scénarios. Lorsque les messages provenant de plusieurs appareils sont transférés au hub à l’aide du même ID de périphérique de passerelle, ils arrivent dans la même partition, ce qui peut entraîner un dépassement de la limite d’échelle par partition. 

**Impact** : Si une partition unique connaît un taux d’ingestion soutenu au-delà de la limite de la préversion, il est possible que le lecteur TSI ne rattrape jamais son retard avant que la période de conservation des données IoT Hub ne soit dépassée. Cela entraînerait une perte de données.

Nous recommandons ce qui suit : 

* Calculez votre taux d’ingestion par environnement et par partition avant de déployer votre solution
* Assurez-vous que vos appareils IoT Hub (et par conséquent les partitions) sont équilibrés par leur charge dans toute la mesure du possible

> [!WARNING]
> Pour les environnements qui utilisent IoT Hub comme source d’événements, calculez le taux d’ingestion à l’aide du nombre de hubs en cours d’utilisation pour vous assurer que le taux tombe sous la limite de 0,5 Mbits/s par partition de la préversion.

  ![Diagramme de partitions IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Pour plus d’informations sur les unités de débit et les partitions, consultez les liens suivants :

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
> Pendant la préversion, il peut s’écouler jusqu’à 60 secondes avant que les données ne soient disponibles. Si vous constatez une latence significative supérieure à 60 secondes, veuillez envoyer un ticket de support par le biais du Portail Azure.

## <a name="azure-storage"></a>Stockage Azure

Cette section décrit les détails du stockage Azure relatifs à Azure Time Series Insights (préversion).

Pour obtenir une description complète du stockage Blob Azure, lisez l’[introduction aux objets blob de stockage](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Votre compte de stockage

Lorsque vous créez un environnement Time Series Insights (préversion) pour un paiement à l’utilisation, un compte d’objets blob GPv1 Stockage Azure est créé en tant que magasin froid à long terme.  

Time Series Insights (préversion) publie jusqu’à deux copies de chaque événement dans votre compte de Stockage Azure. La copie initiale possède des événements classés par heure d’ingestion et est toujours conservée, ce qui vous permet d’utiliser d’autres services pour y accéder. Vous pouvez utiliser Spark, Hadoop et d’autres outils familiers pour traiter les fichiers Parquet bruts. 

Time Series Insights (préversion) repartitionne les fichiers Parquet afin d’optimiser la requête Time Series Insights. Cette copie repartitionnée des données est également enregistrée.

Lors de la préversion publique, les données sont stockées indéfiniment dans votre compte de Stockage Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Écriture et modification des objets blob Time Series Insights

Pour garantir les performances des requêtes et la disponibilité des données, ne modifiez ni ne supprimez aucun objet blob créé par Time Series Insights (préversion).

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accès et exportation des données à partir de Time Series Insights - Préversion

Vous souhaiterez peut-être accéder aux données affichées dans l’explorateur de Time Series Insights (préversion) pour les utiliser conjointement avec d’autres services. Par exemple, vous pouvez utiliser vos données pour générer un rapport dans Power BI ou pour effectuer l’apprentissage d’un modèle Machine Learning à l’aide d’Azure Machine Learning Studio. Vous pouvez également utiliser vos données pour transformer, visualiser et modéliser vos Jupyter Notebooks.

Vous pouvez accéder à vos données au moyen de trois méthodes générales :

* À partir de l’explorateur Time Series Insights - Préversion. Vous pouvez exporter des données en tant que fichier CSV à partir de l’explorateur. Pour plus d’informations, consultez [Explorateur Time Series Insights (préversion)](./time-series-insights-update-explorer.md).
* À partir de l’API Time Series Insights (préversion) à l’aide de la requête Get Events. Pour en savoir plus sur cette API, consultez [Requête de série chronologique](./time-series-insights-update-tsq.md).
* Directement à partir d’un compte de Stockage Azure. Vous avez besoin d’un accès en lecture pour tous les comptes que vous utilisez afin d’accéder à vos données Time Series Insights (préversion). Pour en savoir plus, consultez [Gérer l’accès aux ressources de votre compte de stockage](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Suppression des données

Ne supprimez pas vos fichiers Time Series Insights (préversion). Gérez les données associées à partir de Time Series Insights (préversion) uniquement.

### <a name="parquet-file-format-and-folder-structure"></a>Format de fichier Parquet et structure de dossiers

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
* Chaque ligne comprend les colonnes ID Time Series telle qu’elles sont définies lors de la création de l’environnement Time Series Insights. Le nom de la propriété comprend le suffixe `_string`.
* Toutes les autres propriétés envoyées en tant que données de télémétrie sont mappées à des noms de colonnes qui se terminent par `_string` (chaîne), `_bool` (booléen), `_datetime` (Date/Heure) et `_double` (double), selon le type de propriété.
* Ce schéma de mappage s’applique à la première version du format de fichier, référencée comme **V=1**. Avec l’évolution de cette fonctionnalité, le nom est susceptible d’être incrémenté.

## <a name="next-steps"></a>Étapes suivantes

- Lisez [Comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).

- En savoir plus sur la nouvelle [modélisation des données](./time-series-insights-update-tsm.md).
