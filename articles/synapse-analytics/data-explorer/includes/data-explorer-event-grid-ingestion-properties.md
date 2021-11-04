---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 73cd54f16e29564fda5f9e41d0469d59ea99526d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478962"
---
|**Propriété** | **Description de la propriété**|
|---|---|
| `rawSizeBytes` | Taille des données brutes (non compressées). Pour Avro/ORC/Parquet, il s’agit de la taille avant l’application de la compression spécifique au format. Indiquez la taille des données d’origine en affectant à cette propriété la taille des données non compressées en octets.|
| `kustoTable` |  Nom de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| `kustoDataFormat` |  Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| `kustoIngestionMappingReference` |  Nom du mappage d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| `kustoIgnoreFirstRecord` | Si la valeur définie est `true`, Kusto ignore la première ligne de l’objet blob. Utilisez dans les données au format tabulaire (CSV, TSV ou similaire) pour ignorer les en-têtes. |
| `kustoExtentTags` | Chaîne représentant les [balises](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context) qui seront attachées à l’étendue résultante. |
| `kustoCreationTime` |  Remplace [$IngestionTime](/azure/data-explorer/kusto/query/ingestiontimefunction?context=/azure/synapse-analytics/context/context?pivots=azuredataexplorer) pour l’objet blob, au format d’une chaîne ISO 8601. À utiliser pour le renvoi. |
