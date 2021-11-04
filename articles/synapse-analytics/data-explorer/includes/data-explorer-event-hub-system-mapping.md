---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e7e723394bd7a90f6fd4cda8db7c7cb27c4cb9a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479035"
---
> [!NOTE]
> * Les propriétés système sont prises en charge pour les formats `json` et tabulaires (`csv`, `tsv`, etc.), mais pas sur les données compressées. Quand vous utilisez un format non pris en charge, les données sont toujours ingérées, mais les propriétés sont ignorées.
> * Pour les données tabulaires, les propriétés système sont prises en charge uniquement pour les messages d’événements à enregistrement unique.
> * Pour les données JSON, les propriétés système sont également prises en charge pour les messages d’événements à enregistrements multiples. Dans ce cas, les propriétés système sont ajoutées uniquement au premier enregistrement du message d’événement. 
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans la table [Propriétés système](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties).
> * Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans la table [Propriétés système](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties).
