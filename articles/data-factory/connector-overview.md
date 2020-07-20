---
title: Vue d’ensemble du connecteur Azure Data Factory
description: Découvrez les connecteurs pris en charge dans Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: d7a872121ca6560b8ede86abc35294ab8c9b0c1b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142003"
---
# <a name="azure-data-factory-connector-overview"></a>Vue d’ensemble du connecteur Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory prend en charge les magasins de données et les formats suivants par le biais des activités de copie, de flux de données, de recherche, d’obtention de métadonnées et de suppression. Cliquez sur chaque banque de données pour découvrir les fonctionnalités prises en charge et les configurations correspondantes en détail.

## <a name="supported-data-stores"></a>Magasins de données pris en charge

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Formats de fichiers pris en charge

Azure Data Factory prend en charge les formats de fichier suivants. Reportez-vous à chaque article pour les paramètres basés sur le format.

- [Format Avro](format-avro.md)
- [Format binaire](format-binary.md)
- [Format Common Data Model](format-common-data-model.md)
- [Format de texte délimité](format-delimited-text.md)
- [Format Excel](format-excel.md)
- [Format JSON](format-json.md)
- [Format ORC](format-orc.md)
- [Format Parquet](format-parquet.md)

## <a name="next-steps"></a>Étapes suivantes

- [Activité de copie](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)
