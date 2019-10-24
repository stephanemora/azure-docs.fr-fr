---
title: Accès multi-protocole sur Azure Data Lake Storage (préversion) | Microsoft Docs
description: Utilisez des API d’objets BLOB et des applications qui utilisent des API d’objets BLOB avec Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 9767282b3dd764a45f25a14d62af70a13c80b0ac
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300255"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Accès multi-protocole sur Azure Data Lake Storage (préversion)

Les API d’objets BLOB fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Cela déverrouille l’intégralité de l’écosystème des outils, des applications et des services, ainsi que toutes les fonctionnalités de stockage d’objets BLOB pour les comptes qui ont un espace de noms hiérarchique.

Jusqu’à récemment, vous avez peut-être dû maintenir des solutions de stockage distinctes pour le stockage des objets et l’analyse du stockage. Cela est dû au fait que Azure Data Lake Storage Gen2 avait une prise en charge limitée de l’écosystème. Il avait également un accès limité aux fonctionnalités du service BLOB, telles que la journalisation des diagnostics. Une solution de stockage fragmenté est difficile à maintenir, car vous devez déplacer les données entre les comptes pour effectuer différents scénarios. Vous n’avez plus à le faire.

> [!NOTE]
> L’accès multi-protocole sur Data Lake Storage est en préversion publique et disponible dans toutes les régions. Vous n’êtes pas obligé de vous inscrire à la préversion publique, car elle est disponible automatiquement pour tous les comptes qui ont un espace de noms hiérarchique. Pour passer en revue les limitations, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Utiliser l’intégralité de l’écosystème d’applications, d’outils et de services

Avec l’accès multi-protocole sur Data Lake Storage, vous pouvez travailler avec toutes vos données à l’aide de l’écosystème complet d’outils, d’applications et de services. Cela comprend les services Azure tels que[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/),[Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources) et bien d’autres. Pour obtenir une liste complète, consultez [Intégrer Azure Data Lake Storage aux services Azure](data-lake-store-integrate-with-azure-services.md).

Cela comprend également des outils et des applications tiers. Vous pouvez les rediriger vers des comptes qui ont un espace de noms hiérarchique sans avoir à les modifier. Ces applications fonctionnent *comme telles* même si elles appellent des API d’objets BLOB, car les API d’objets BLOB peuvent désormais fonctionner sur des données de comptes qui ont un espace de noms hiérarchique.

> [!NOTE]
> Pour passer en revue les limitations, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="use-all-blob-storage-features"></a>Utilisez toutes les fonctionnalités de stockage d’objets BLOB

Les fonctionnalités de stockage d’objets BLOB, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](storage-blob-storage-tiers.md) et les [stratégies de gestion du cycle de vie du stockage BLOB](storage-lifecycle-management-concepts.md), fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Par conséquent, vous pouvez activer des espaces de noms hiérarchiques sur vos comptes de stockage d’objets BLOB sans perdre l’accès à ces fonctionnalités importantes. 

> [!NOTE]
> Pour passer en revue les limitations, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Comment fonctionne l’accès multi-protocole pour Azure Data Lake Storage

Les API d’objets BLOB et les API Data Lake Storage Gen2 peuvent fonctionner sur les mêmes données dans les comptes de stockage qui ont un espace de noms hiérarchique. Data Lake Storage Gen2 achemine les API d’objets BLOB via l’espace de noms hiérarchique afin que vous puissiez bénéficier des avantages des premières opérations de répertoire de classe et des listes de contrôle d’accès (ACL) compatibles avec POSIX. 

![Accès multi-protocole sur Data Lake Storage conceptuelle](./media/data-lake-storage-interop/interop-concept.png) 

Les outils et les applications existants qui utilisent l’API BLOB bénéficient automatiquement de ces avantages. Les développeurs ne sont pas obligés de les modifier. Data Lake Storage Gen2 applique de manière cohérente les listes de contrôle d’accès au niveau des répertoires et des fichiers, quel que soit le protocole utilisé par les outils et les applications pour accéder aux données. 

## <a name="next-steps"></a>Étapes suivantes

Consultez [Problèmes connus](data-lake-storage-known-issues.md)




