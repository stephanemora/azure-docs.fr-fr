---
title: Accès multiprotocole sur Azure Data Lake Storage | Microsoft Docs
description: Utilisez des API d’objets BLOB et des applications qui utilisent des API d’objets BLOB avec Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: aebd7b58a2107ac109a03d3ce12f27d65ba0ab90
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769810"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Accès multi-protocole pour Azure Data Lake Storage

Les API d’objets BLOB fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Cela déverrouille de l’écosystème des outils, des applications et des services, ainsi que de nombreuses fonctionnalités de stockage d’objets BLOB pour les comptes qui ont un espace de noms hiérarchique.

Jusqu’à récemment, vous avez peut-être dû maintenir des solutions de stockage distinctes pour le stockage des objets et l’analyse du stockage. Cela est dû au fait que Azure Data Lake Storage Gen2 avait une prise en charge limitée de l’écosystème. Il avait également un accès limité aux fonctionnalités du service BLOB, telles que la journalisation des diagnostics. Une solution de stockage fragmenté est difficile à maintenir, car vous devez déplacer les données entre les comptes pour effectuer différents scénarios. Vous n’avez plus à le faire.

Avec l’accès multi-protocole sur Data Lake Storage, vous pouvez travailler avec vos données à l’aide de l’écosystème d’outils, d’applications et de services. Cela comprend également des outils et des applications tiers. Vous pouvez les rediriger vers des comptes qui ont un espace de noms hiérarchique sans avoir à les modifier. Ces applications fonctionnent *comme telles* même si elles appellent des API d’objets BLOB, car les API d’objets BLOB peuvent désormais fonctionner sur des données de comptes qui ont un espace de noms hiérarchique.

Les fonctionnalités de stockage d’objets BLOB, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](storage-blob-storage-tiers.md) et les [stratégies de gestion du cycle de vie du stockage BLOB](storage-lifecycle-management-concepts.md), fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Par conséquent, vous pouvez activer des espaces de noms hiérarchiques sur vos comptes de stockage d’objets BLOB sans perdre l’accès à ces fonctionnalités importantes. 

> [!NOTE]
> L’accès multi-protocole sur Data Lake Storage est en disponibilité générale dans toutes les régions. Certains services Azure et certaines fonctionnalités de stockage Blob que l’accès multiprotocole active restent en version préliminaire. Pour plus d’informations, consultez les tableaux de chaque section de cet article. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Comment fonctionne l’accès multi-protocole pour Azure Data Lake Storage

Les API d’objets BLOB et les API Data Lake Storage Gen2 peuvent fonctionner sur les mêmes données dans les comptes de stockage qui ont un espace de noms hiérarchique. Data Lake Storage Gen2 achemine les API d’objets BLOB via l’espace de noms hiérarchique afin que vous puissiez bénéficier des avantages des premières opérations de répertoire de classe et des listes de contrôle d’accès (ACL) compatibles avec POSIX. 

![Accès multi-protocole sur Data Lake Storage conceptuelle](./media/data-lake-storage-interop/interop-concept.png) 

Les outils et les applications existants qui utilisent l’API BLOB bénéficient automatiquement de ces avantages. Les développeurs ne sont pas obligés de les modifier. Data Lake Storage Gen2 applique de manière cohérente les listes de contrôle d’accès au niveau des répertoires et des fichiers, quel que soit le protocole utilisé par les outils et les applications pour accéder aux données. 

## <a name="blob-storage-feature-support"></a>Prise en charge de la fonctionnalité Stockage Blob

L’accès multiprotocole sur Data Lake Storage vous permet d’utiliser davantage de fonctionnalités de stockage d’objets Blob avec votre Data Lake Storage. Ce tableau répertorie les fonctionnalités qui sont activées par l’accès multiprotocole sur Data Lake Storage. 

Les éléments qui apparaissent dans ce tableau changeront au fil du temps, car la prise en charge des fonctionnalités de stockage d’objets Blob continue à s’étendre. 

> [!NOTE]
> Si l’accès multiprotocole sur Data Lake Storage est en disponibilité générale, la prise en charge de certaines de ces fonctionnalités reste en version préliminaire. 

|Fonctionnalité Stockage Blob | Niveau de support |
|---|---|
|[Niveau d’accès froid](storage-blob-storage-tiers.md)|Mise à la disposition générale|
|API REST de blobs|Mise à la disposition générale|
|SDK d’objets Blob |Mise à la disposition générale|
|[PowerShell (Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Mise à la disposition générale|
|[CLI (Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Mise à la disposition générale|
|Les SDK des objets blob avec la sémantique du système de fichiers ([.NET](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|PRÉVERSION|
|[PowerShell avec la sémantique du système de fichiers](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|PRÉVERSION|
|[CLI avec la sémantique du système de fichiers](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|PRÉVERSION|
|[Journaux de diagnostic](../common/storage-analytics-logging.md)| PRÉVERSION|
|[Stratégies de gestion du cycle de vie](storage-lifecycle-management-concepts.md)| PRÉVERSION|
|[Notifications via Azure Event Grid](data-lake-storage-events.md)|PRÉVERSION|
|[Archive access tier](storage-blob-storage-tiers.md) (Niveau d’accès archive)| PRÉVERSION|
|[blobfuse](storage-how-to-mount-container-linux.md)|Pas encore pris en charge|
|[Stockage non modifiable](storage-blob-immutable-storage.md)|Pas encore pris en charge|
|[Instantanés](storage-blob-snapshots.md)|Pas encore pris en charge|
|[Suppression réversible](storage-blob-soft-delete.md)|Pas encore pris en charge|
|[Sites web statiques](storage-blob-static-website.md)|Pas encore pris en charge|

Pour en savoir plus sur les problèmes connus et les limitations d’Azure Data Lake Storage Gen2, consultez [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Prise en charge de l’écosystème Azure

L’accès multiprotocole sur Data Lake Storage vous permet également de connecter plus de services Azure à votre Data Lake Storage. Ce tableau répertorie les services qui sont activés par l’accès multiprotocole sur Data Lake Storage. 

Comme pour les fonctionnalités de stockage d’objets Blob, les éléments qui apparaissent dans ce tableau changeront au fil du temps, car la prise en charge des services Azure continue à s’étendre. 

> [!NOTE]
> Si l’accès multiprotocole sur Data Lake Storage est en disponibilité générale, la prise en charge de certains de ces services reste en version préliminaire. 

|Service Azure | Niveau de support |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Mise à la disposition générale|
|[Azure Event Hubs Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Mise à la disposition générale|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Mise à la disposition générale|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Mise à la disposition générale|
|[Logic Apps](https://azure.microsoft.com/services/logic-apps/)|Mise à la disposition générale|
|[Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|PRÉVERSION|

Pour obtenir la liste complète de la prise en charge de l’écosystème Azure avec Azure Data Lake Storage Gen2, consultez [Intégrer Azure Data Lake Storage avec les services Azure](data-lake-storage-integrate-with-azure-services.md).

Pour en savoir plus sur les problèmes connus et les limitations d’Azure Data Lake Storage Gen2, consultez [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez [Problèmes connus](data-lake-storage-known-issues.md)




