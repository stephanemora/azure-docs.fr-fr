---
title: Vue d’ensemble du stockage - Azure Time Series Insights Gen2 | Microsoft Docs
description: Découvrez le stockage des données dans Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483267"
---
# <a name="data-storage"></a>Stockage des données

Lorsque vous créez un environnement Azure Time Series Insights Gen2, vous créez deux ressources Azure :

* Un environnement Azure Time Series Insights Gen2 qui peut être configuré pour un stockage chaud des données.
* Un compte Stockage Azure pour le stockage de données à froid.

Les données de votre magasin chaud sont disponibles uniquement via les [API Time Series Query](./time-series-insights-update-tsq.md) et l’[Explorateur Time Series Insights](./time-series-insights-update-explorer.md). Votre magasin chaud contiendra les données récentes soumises à la [période de rétention](./time-series-insights-update-plan.md#the-preview-environment) sélectionnée lors de la création de l’environnement Azure Time Series Insights Gen2.

Azure Time Series Insights Gen2 enregistre les données de votre magasin froid dans le stockage Blob Azure au [format de fichier Parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 gère ces données de magasin froid en exclusivité, mais vous pouvez les lire directement en tant que fichiers Parquet standard.

> [!WARNING]
> En tant que propriétaire du compte de stockage Blob Azure où résident les données du magasin froid, vous avez un accès total à toutes les données du compte. Cet accès comprend des autorisations d’écriture et de suppression. Ne modifiez pas ni ne supprimez les données qu’Azure Time Series Insights Gen2 écrit, car cela peut entraîner une perte de données.

## <a name="data-availability"></a>Disponibilité des données

Azure Time Series Insights Gen2 partitionne et indexe les données pour optimiser les performances des requêtes. Il est possible d’interroger les données après les avoir indexées à la fois depuis un magasin chaud (s’il est activé) et un magasin froid. La quantité de données en cours d’ingestion peut avoir une incidence sur cette disponibilité.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à 60 secondes avant que les données ne soient disponibles. Si vous constatez une latence significative supérieure à 60 secondes, veuillez envoyer un ticket de support par le biais du Portail Azure.

## <a name="azure-storage"></a>Stockage Azure

Cette section décrit les détails du stockage Azure relatifs à Azure Time Series Insights Gen2.

Pour obtenir une description complète du stockage Blob Azure, lisez l’[introduction aux objets blob de stockage](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Votre compte de stockage

Lorsque vous créez un environnement pour Azure Time Series Insights Gen2, un compte Stockage Azure est créé en tant que magasin froid à long terme.  

Azure Time Series Insights Gen2 conserve jusqu’à deux copies de chaque événement dans votre compte Stockage Azure. Une copie stocke les événements classés par heure d’ingestion, autorisant toujours l’accès aux événements dans un ordre chronologique. Au fil du temps, Azure Time Series Insights Gen2 crée également une copie repartitionnée des données à optimiser pour des requêtes performantes.

Toutes vos données sont stockées indéfiniment dans votre compte de Stockage Azure.

#### <a name="writing-and-editing-blobs"></a>Création et modification de d’objets blob

Pour garantir les performances des requêtes et la disponibilité des données, ne modifiez ni ne supprimez aucun objet blob créé par Azure Time Series Insights Gen2.

#### <a name="accessing-cold-store-data"></a>Accès aux données du magasin froid

En plus d’accéder à vos données à partir de l’[Explorateur Time Series Insights](./time-series-insights-update-explorer.md) et des [API Time Series Query](./time-series-insights-update-tsq.md), vous pouvez également accéder à vos données directement à partir des fichiers Parquet stockés dans le magasin froid. Par exemple, vous pouvez lire, transformer et nettoyer les données d’un notebook Jupyter, puis les utiliser pour effectuer l’apprentissage de votre modèle Azure Machine Learning dans le même workflow Spark.

Pour accéder aux données directement à partir de votre compte Stockage Azure, vous avez besoin d’un accès en lecture au compte utilisé pour stocker vos données Azure Time Series Insights Gen2. Vous pouvez ensuite lire les données sélectionnées en fonction de l’heure de création du fichier Parquet situé dans le dossier `PT=Time` décrit ci-dessous dans la section [Format de fichier Parquet](#parquet-file-format-and-folder-structure).  Pour plus d’informations sur l’activation de l’accès en lecture à votre compte de stockage, consultez [Gérer l’accès aux ressources de votre compte de stockage](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Suppression des données

Ne supprimez pas vos fichiers Azure Time Series Insights Gen2. Gérez les données associées à partir d’Azure Time Series Insights Gen2 uniquement.

### <a name="parquet-file-format-and-folder-structure"></a>Format de fichier Parquet et structure de dossiers

Parquet est un format de fichier en colonnes open source qui a été conçu pour un stockage et des performances efficaces. Azure Time Series Insights Gen2 utilise Parquet pour permettre la performance des requêtes basées sur l’ID Time Series à grande échelle.  

Pour plus d’informations sur le type de fichier Parquet, consultez la [documentation relative au format Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 stocke des copies de vos données comme suit :

* La première copie initiale est partitionnée par heure d’ingestion et stocke les données par ordre approximatif d’arrivée. Ces données se trouvent dans le dossier `PT=Time` :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La deuxième copie repartitionnée est regroupée par ID Time Series et se trouve dans le dossier `PT=TsId` :

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Le timestamp des noms d’objets blob du dossier `PT=Time` correspond à l’heure d’arrivée des données dans Azure Time Series Insights Gen2 et non au timestamp des événements.

Les données du dossier `PT=TsId` seront optimisées pour les requêtes au fil du temps et ne sont pas statiques. Durant le repartitionnement, plusieurs événements peuvent être présents dans plusieurs objets blob. Il n’est pas garanti que le nom des objets blob dans ce dossier reste le même.

En règle générale, si vous avez besoin d’accéder directement aux données via des fichiers Parquet, utilisez le dossier `PT=Time`.  Les fonctionnalités futures permettront un accès efficace au dossier `PT=TsId`.

> [!NOTE]
>
> * `<YYYY>` correspond à une représentation de l’année à quatre chiffres.
> * `<MM>` correspond à une représentation du mois à deux chiffres.
> * `<YYYYMMDDHHMMSSfff>` correspond à une représentation de l’horodatage avec une année à quatre chiffres (`YYYY`), un mois à deux chiffres (`MM`), un jour à deux chiffres (`DD`), une heure à deux chiffres (`HH`), une minute à deux chiffres (`MM`), une seconde à deux chiffres (`SS`) et une milliseconde à trois chiffres (`fff`).

Les événements Azure Time Series Insights Gen2 sont mappés au contenu du fichier Parquet comme suit :

* Chaque événement correspond à une ligne unique.
* Chaque ligne comprend la colonne **timestamp** avec un horodatage de l’événement. La propriété timestamp n’est jamais nulle. Par défaut, elle a la valeur **event enqueued time** si la propriété timestamp n’est pas spécifiée dans la source de l’événement. L’horodatage stocké est toujours au format UTC.
* Chaque ligne comprend les colonnes ID Time Series (TSID) telles qu’elles sont définies lors de la création de l’environnement Azure Time Series Insights Gen2. Le nom de la propriété TSID comprend le suffixe `_string`.
* Toutes les autres propriétés envoyées en tant que données de télémétrie sont mappées à des noms de colonnes qui se terminent par `_bool` (booléen), `_datetime` (horodatage), `_long` (long), `_double` (double), `_string` (chaîne) ou `dynamic` (dynamique), en fonction du type de propriété.  Pour en savoir plus, découvrez les [types de données pris en charge](./concepts-supported-data-types.md).
* Ce schéma de mappage s’applique à la première version du format de fichier, référencée **V=1** et stockée dans le dossier de base du même nom. À mesure que cette fonctionnalité évolue, ce schéma de mappage est susceptible de changer et le nom de référence d’être incrémenté.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [modélisation des données](./time-series-insights-update-tsm.md).

* Planifier votre [environnement Azure Time Series Insights Gen2](./time-series-insights-update-plan.md).
