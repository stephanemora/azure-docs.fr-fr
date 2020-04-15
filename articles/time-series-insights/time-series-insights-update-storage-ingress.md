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
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 95a579cacc339360295f5f25fa6415ab29cd68ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673899"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrée et stockage des données dans Azure Time Series Insights - Préversion

Cet article décrit les mises à jour de l’entrée et du stockage des données pour Azure Time Series Insights (préversion). Il décrit la structure de stockage sous-jacente, le format de fichier et la propriété ID de série chronologique. Il décrit également le processus d’entrée sous-jacent, les meilleures pratiques et les limitations de la préversion.

## <a name="data-ingress"></a>Entrée de données

Votre environnement Azure Time Series Insights contient un *moteur d’ingestion* pour collecter, traiter et stocker des données de série chronologique. 

Vous devez garder certaines considérations à l’esprit pour vous assurer que toutes les données entrantes sont traitées, atteindre une grande échelle d’entrée et réduire la *latence d’ingestion* (temps que Time Series Insights met à lire et à traiter les données de la source de l’événement) lors de la [planification de votre environnement](time-series-insights-update-plan.md).

Les stratégies d’entrée de données de la préversion de Time Series Insights déterminent la source possible de données et le format de celles-ci.

### <a name="ingress-policies"></a>Stratégies d’entrée

L’*entrée de données* implique la manière dont les données sont envoyées à un environnement en préversion d’Azure Time Series Insights. 

La configuration, la mise en forme et les meilleures pratiques clés sont résumées ci-dessous.

#### <a name="event-sources"></a>Sources de l’événement

La préversion d’Azure Time Series Insights prend en charge les sources d’événements suivantes :

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

La préversion d’Azure Time Series Insights prend en charge un maximum de deux sources d’événements par instance. Lorsque vous connectez une source d'événements, votre environnement TSI lit tous les événements stockés dans votre hub IoT ou Event Hub, en commençant par l'événement le plus ancien. 

> [!IMPORTANT] 
> * Vous pouvez rencontrer une latence initiale élevée lors de la jonction d’une source d’événement à votre environnement en préversion. 
> La latence de la source d’événement dépend du nombre d’événements actuellement présents dans votre IoT Hub ou votre Event Hub.
> * Une latence élevée sera impartie après la réception des données de la source d’événements. Si vous rencontrez une latence élevée, soumettez un ticket de support via le portail Azure.

#### <a name="supported-data-format-and-types"></a>Formats et types de données pris en charge

Azure Time Series Insights prend en charge le format JSON encodé en UTF8 envoyé à partir d’IoT Hub ou d’Azure Event Hubs. 

Les types de données pris en charge sont les suivants :

| Type de données | Description |
|---|---|
| **bool** | Type de données ayant l’un des deux états suivants : `true` ou `false`. |
| **dateTime** | Représente un instant, généralement exprimé sous la forme d’une date ou d’une heure. Valeur exprimée au format [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). |
| **double** | Virgule flottante [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 64 bits à double précision. |
| **string** | Valeurs de texte composées de caractères Unicode.          |

#### <a name="objects-and-arrays"></a>Objets et tableaux

Vous pouvez envoyer des types complexes tels que des objets et des tableaux dans votre charge utile d’événement, mais vos données subissent un processus d’aplatissement lorsqu’elles sont stockées. 

Des informations détaillées décrivant comment mettre en forme vos événements JSON, envoyer un type complexe et aplatir des objets imbriqués sont disponibles dans [Comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md) afin de faciliter la planification et l’optimisation.

### <a name="ingress-best-practices"></a>Meilleures pratiques relatives à l’entrée

Nous vous recommandons d’utiliser les meilleures pratiques suivantes :

* Configurez Azure Time Series Insights et tout IoT Hub ou Event Hub dans la même région pour réduire une latence potentielle.

* [Planifiez vos besoins en matière de mise à l’échelle](time-series-insights-update-plan.md) en calculant le taux d’ingestion escompté et en vérifiant qu’il ne dépasse le taux pris en charge indiqué ci-dessous.

* Découvrez comment optimiser et façonner vos données JSON, ainsi que les limitations actuelles de la préversion, en lisant [comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Limitations de l’échelle d’entrée et de la préversion 

Les limitations d’entrée de la préversion d’Azure Time Series Insights sont décrites ci-dessous.

> [!TIP]
> Pour obtenir la liste complète des limitations de la préversion, voir [Planifier votre environnement de préversion](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits).

#### <a name="per-environment-limitations"></a>Limitations par environnement

En général, les taux d’entrée sont considérés comme le facteur du nombre d’appareils qui se trouvent dans votre organisation, de la fréquence d’émission des événements et de la taille de chaque événement :

*  **Nombre d’appareils** × **Fréquence d’émission d’événements** × **Taille de chaque événement**.

Par défaut, la préversion de Time Series Insights peut ingérer des données entrantes à un débit allant **jusqu’à 1 mégaoctet par seconde (Mbits/s) par environnement Time Series Insights**. Il existe des limitations supplémentaires [par partition de hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP] 
> * Une prise en charge de l’environnement pour l’ingestion de vitesses jusqu’à 16 Mbits/s peut être fournie sur demande.
> * Contactez-nous si vous avez besoin d’un débit supérieur en soumettant un ticket de support via le portail Azure.
 
* **Exemple 1 :**

    Contoso Shipping est doté de 100 000 appareils qui émettent un événement trois fois par minute. La taille d’un événement est de 200 octets. Un hub IoT comportant quatre partitions est utilisé en tant que source d'événements Time Series Insights.

    * Le taux d’ingestion de leur environnement Time Series Insights est le suivant : **100 000 appareils * 200 octets/événement * (3/60 événements/s) = 1 Mbit/s**.
    * Le taux d’ingestion par partition est de 0,25 Mbits/s.
    * Le taux d’ingestion de Contoso Shipping se situe dans la limite de l’échelle de la préversion.

* **Exemple 2 :**

    Contoso Fleet Analytics est doté de 60 000 appareils qui émettent un événement chaque seconde. Un Event Hub comportant quatre partitions est utilisé comme source d'événements Time Series Insights. La taille d’un événement est de 200 octets.

    * Le taux d’ingestion de l’environnement est le suivant : **60 000 appareils * 200 octets/événement * 1 événement/s = 12 Mbit/s**.
    * Le taux par partition est de 3 Mbits/s.
    * Le taux d'ingestion de Contoso Fleet Analytics dépasse les limites de l'environnement et des partitions. Une demande peut être adressée à Time Series Insights via le portail Azure pour augmenter le taux d'ingestion de l'environnement, et un Event Hub comportant davantage de partitions peut être créé afin de respecter les limites de la préversion.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partitions de hub et limites par partition

Lors de la planification de votre environnement Time Series Insights, il est important de tenir compte de la configuration des sources de l’événement que vous comptez connecter à Time Series Insights. IoT Hub et Event Hubs utilisent des partitions afin d’activer la mise à l’échelle horizontale pour le traitement des événements. 

Une *partition* est une séquence ordonnée d’événements conservée dans un hub. Le nombre de partitions est défini lors de la phase de création du hub et ne peut pas être modifié. 

Pour les meilleures pratiques de partitionnement d’Event Hubs, voir [De combien de partitions ai-je besoin ?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> La plupart des IoT Hubs utilisés avec Azure Time Series Insights n’ont besoin que de quatre partitions.

Que vous créiez un hub pour votre environnement Time Series Insights ou en utilisiez un existant, vous devez calculer votre taux d’ingestion par partition pour déterminer s’il s’inscrit dans les limites de la préversion. 

La préversion d’Azure Time Series Insights a actuellement une **limite générale par partition de 0,5 Mbits/s**.

#### <a name="iot-hub-specific-considerations"></a>Considérations spécifiques à IoT Hub

Lors de la création d’un appareil dans IoT Hub, cet appareil est définitivement attribué à une partition. IoT Hub est ainsi en mesure de garantir l’ordre des événements (puisque l’affectation ne change jamais).

Une affectation de partition fixe a également une incidence sur les instances Time Series Insights qui ingèrent les données envoyées à partir de l’IoT Hub en aval. Lorsque des messages provenant de plusieurs appareils sont transférés au hub à l’aide du même ID de passerelle, ils peuvent arriver dans la même partition au même moment, ce qui peut entraîner un dépassement des limites d’échelle par partition. 

**Impact** :

* Si une partition unique subit un taux d’ingestion soutenu supérieur à la limite de la préversion, il est possible que Time Series Insights ne synchronise pas toutes les données de télémétrie des appareils avant que la période de conservation des données IoT Hub soit dépassée. Par conséquent, des données envoyées peuvent se perdre si les limites d’ingestion sont régulièrement dépassées.

Pour limiter ce risque, nous vous recommandons d’adopter les meilleures pratiques suivantes :

* Calculez vos taux d’ingestion par environnement et par partition avant de déployer votre solution.
* Assurez-vous que la charge de vos appareils IoT Hub est équilibrée dans toute la mesure du possible.

> [!IMPORTANT]
> Pour les environnements qui utilisent IoT Hub comme source d’événements, calculez le taux d’ingestion à l’aide du nombre de hubs en cours d’utilisation pour vous assurer que le taux tombe sous la limite de 0,5 Mbits/s par partition de la préversion.
> * Même si plusieurs événements se produisent simultanément, la limite de la préversion n’est pas dépassée.

  ![Diagramme de partitions IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Pour en savoir plus sur l’optimisation du débit et des partitions du hub, reportez-vous aux ressources suivantes :

* [Mise à l’échelle d’IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Mise à l’échelle d’Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partitions Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Stockage des données

Lorsque vous créez un environnement de référence SKU pour la préversion de Time Series Insights assorti d’un *paiement à l’utilisation* (PAYG), vous créez deux ressources Azure :

* Un environnement de préversion Azure Time Series Insights qui peut être configuré pour un stockage chaud des données.
* Un compte d’objets blob GPv1 Stockage Azure pour le stockage froid de données.

Les données de votre magasin chaud sont disponibles uniquement via [Time Series Query](./time-series-insights-update-tsq.md) et l’[explorateur d’Azure Time Series Insights (préversion)](./time-series-insights-update-explorer.md). Votre magasin chaud contiendra les données récentes soumises à la [période de rétention](./time-series-insights-update-plan.md#the-preview-environment) sélectionnée lors de la création de l’environnement Time Series Insights.

Time Series Insights (préversion) enregistre les données de votre magasin froid dans le stockage Blob Azure au [format de fichier Parquet](#parquet-file-format-and-folder-structure). Time Series Insights (préversion) gère ces données de magasin froid en exclusivité, mais vous pouvez les lire directement en tant que fichiers Parquet standard.

> [!WARNING]
> En tant que propriétaire du compte de stockage Blob Azure où résident les données du magasin froid, vous avez un accès total à toutes les données du compte. Cet accès comprend des autorisations d’écriture et de suppression. Ne modifiez pas ni ne supprimez les données que Time Series Insights (préversion) écrit, car cela peut entraîner une perte de données.

### <a name="data-availability"></a>Disponibilité des données

La préversion d’Azure Time Series Insights partitionne et indexe les données pour optimiser les performances des requêtes. Il est possible d’interroger les données après les avoir indexées à la fois depuis un magasin chaud (s’il est activé) et un magasin froid. La quantité de données en cours d’ingestion peut avoir une incidence sur cette disponibilité.

> [!IMPORTANT]
> Pendant la préversion, il peut s’écouler jusqu’à 60 secondes avant que les données ne soient disponibles. Si vous constatez une latence significative supérieure à 60 secondes, veuillez envoyer un ticket de support par le biais du Portail Azure.

## <a name="azure-storage"></a>Stockage Azure

Cette section décrit les détails du stockage Azure relatifs à Azure Time Series Insights (préversion).

Pour obtenir une description complète du stockage Blob Azure, lisez l’[introduction aux objets blob de stockage](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Votre compte de stockage

Lorsque vous créez un environnement pour la préversion d’Azure Time Series Insights assorti d’un paiement à l’utilisation (PAYG), un compte d’objets blob GPv1 de Stockage Azure est créé en tant que magasin froid à long terme.  

La préversion d’Azure Time Series Insights conserve jusqu’à deux copies de chaque événement dans votre compte Stockage Azure. Une copie stocke les événements classés par heure d’ingestion, autorisant toujours l’accès aux événements dans un ordre chronologique. Au fil du temps, Time Series Insights (préversion) crée également une copie repartitionnée des données à optimiser pour une interrogation performante de Time Series Insights. 

Pendant la durée de la préversion publique, les données sont stockées indéfiniment dans votre compte de Stockage Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Écriture et modification des objets blob Time Series Insights

Pour garantir les performances des requêtes et la disponibilité des données, ne modifiez ni ne supprimez aucun objet blob créé par Time Series Insights (préversion).

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Accès aux données du magasin froid Time Series Insights (préversion) 

En plus d’accéder à vos données à partir de l’[Explorateur Time Series Insights (préversion)](./time-series-insights-update-explorer.md) et de [Time Series Query](./time-series-insights-update-tsq.md), vous pouvez également accéder à vos données directement à partir des fichiers Parquet stockés dans le magasin froid. Par exemple, vous pouvez lire, transformer et nettoyer les données d’un notebook Jupyter, puis les utiliser pour effectuer l’apprentissage de votre modèle Azure Machine Learning dans le même workflow Spark.

Pour accéder aux données directement à partir de votre compte Stockage Azure, vous avez besoin d’un accès en lecture au compte utilisé pour stocker vos données Time Series Insights (préversion). Vous pouvez ensuite lire les données sélectionnées en fonction de l’heure de création du fichier Parquet situé dans le dossier `PT=Time` décrit ci-dessous dans la section [Format de fichier Parquet](#parquet-file-format-and-folder-structure).  Pour plus d’informations sur l’activation de l’accès en lecture à votre compte de stockage, consultez [Gérer l’accès aux ressources de votre compte de stockage](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Suppression des données

Ne supprimez pas vos fichiers Time Series Insights (préversion). Gérez les données associées à partir de Time Series Insights (préversion) uniquement.

### <a name="parquet-file-format-and-folder-structure"></a>Format de fichier Parquet et structure de dossiers

Parquet est un format de fichier en colonnes open source qui a été conçu pour un stockage et des performances efficaces. Time Series Insights (préversion) utilise Parquet pour permettre la performance des requêtes basées sur l’ID Time Series à grande échelle.  

Pour plus d’informations sur le type de fichier Parquet, consultez la [documentation relative au format Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights (préversion) stocke des copies de vos données comme suit :

* La première copie initiale est partitionnée par heure d’ingestion et stocke les données par ordre approximatif d’arrivée. Ces données se trouvent dans le dossier `PT=Time` :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La deuxième copie repartitionnée est regroupée par ID Time Series et se trouve dans le dossier `PT=TsId` :

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Dans les deux cas, la propriété Time du fichier Parquet correspond à l’heure de création du blob. Les données du dossier `PT=Time` sont conservées sans aucune modification une fois qu’elles sont écrites dans le fichier. Les données du dossier `PT=TsId` seront optimisées pour les requêtes au fil du temps et ne sont pas statiques.

> [!NOTE]
> * `<YYYY>` correspond à une représentation de l’année à quatre chiffres.
> * `<MM>` correspond à une représentation du mois à deux chiffres.
> * `<YYYYMMDDHHMMSSfff>` correspond à une représentation de l’horodatage avec une année à quatre chiffres (`YYYY`), un mois à deux chiffres (`MM`), un jour à deux chiffres (`DD`), une heure à deux chiffres (`HH`), une minute à deux chiffres (`MM`), une seconde à deux chiffres (`SS`) et une milliseconde à trois chiffres (`fff`).

Les événements Time Series Insights (préversion) sont mappés au contenu du fichier Parquet comme suit :

* Chaque événement correspond à une ligne unique.
* Chaque ligne comprend la colonne **timestamp** avec un horodatage de l’événement. La propriété timestamp n’est jamais nulle. Par défaut, elle a la valeur **event enqueued time** si la propriété timestamp n’est pas spécifiée dans la source de l’événement. L’horodatage stocké est toujours au format UTC.
* Chaque ligne comprend les colonnes ID Time Series (TSID) telles qu’elles sont définies lors de la création de l’environnement Time Series Insights. Le nom de la propriété TSID comprend le suffixe `_string`.
* Toutes les autres propriétés envoyées en tant que données de télémétrie sont mappées à des noms de colonnes qui se terminent par `_string` (chaîne), `_bool` (booléen), `_datetime` (Date/Heure) et `_double` (double), selon le type de propriété.
* Ce schéma de mappage s’applique à la première version du format de fichier, référencée **V=1** et stockée dans le dossier de base du même nom. À mesure que cette fonctionnalité évolue, ce schéma de mappage est susceptible de changer et le nom de référence d’être incrémenté.

## <a name="next-steps"></a>Étapes suivantes

- Lisez [Comment mettre en forme JSON pour l’entrée et la requête](./time-series-insights-update-how-to-shape-events.md).

- En savoir plus sur la nouvelle [modélisation des données](./time-series-insights-update-tsm.md).
