---
title: COVID-19 Data Lake
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le lac de données COVID-19 Data Lake dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e43ea946511efaa313204ca26bf7e05f1b3f923a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038643"
---
# <a name="covid-19-data-lake"></a>COVID-19 Data Lake

Le lac de données COVID-19 Data Lake contient des jeux de données liés à la pandémie de COVID-19 provenant de diverses sources. Il couvre des données de suivi de l’évolution de l’état de santé et de test des patients, la stratégie de distanciation sociale, les capacités hospitalières, la mobilité, etc.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Le COVID-19 Data Lake est hébergé dans Azure Data Lake Storage dans la région USA Est. Pour chaque jeu de données, les versions modifiées aux formats csv, json, json-lines et parquet sont disponibles. Les données brutes sont également disponibles en tant que données ingérées.

Les codes de subdivision ISO 3166 sont ajoutés là où ils manquaient pour simplifier la jonction. Noms de colonnes reformatés en minuscules avec traits de soulignement comme séparateurs. Les jeux de données sont mis à jour quotidiennement. Des copies historiques des fichiers modifiés et bruts sont également disponibles.

## <a name="datasets"></a>Groupes de données

| Groupes de données                                                                 | Description                                                                                                                                                                                                                                             |
|--------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Données Bing COVID-19](dataset-bing-covid-19.md)                                                       | Les données COVID-19 de Bing incluent les cas confirmés, décédés et guéris de toutes les régions, mis à jour quotidiennement.                                                                                                                                                      |
| [Projet de suivi COVID Tracking](dataset-covid-tracking.md)                                                | Le jeu de données du projet COVID Tracking fournit les derniers chiffres sur les tests, les cas confirmés, les hospitalisations et l’état des patients pour chaque État américain et l’ensemble du territoire.                                                                                     |
| [Centre européen pour la prévention et le contrôle des maladies (CEPCM) - Cas de COVID-19](dataset-ecdc-covid-cases.md) | Dernières données publiques disponibles sur la répartition géographique des cas de COVID-19 dans le monde, fournies par le Centre européen de prévention et de contrôle des maladies (ECDC). Chaque ligne/entrée contient le nombre de nouveaux cas signalés par jour et par pays ou région. |
| [Oxford - Suivi des mesures gouvernementales face à la pandémie de COVID-19](dataset-oxford-covid-government-response-tracker.md)                              | Le jeu de données OxCGRT (Oxford Covid-19 Government Response Tracker) contient des informations systématiques sur les gouvernements qui ont pris telle ou telle mesure, et à quel moment.                                                                                              |

---

## <a name="next-steps"></a>Étapes suivantes

Affichez les autres jeux de données dans le [Catalogue Open Datasets](dataset-catalog.md).