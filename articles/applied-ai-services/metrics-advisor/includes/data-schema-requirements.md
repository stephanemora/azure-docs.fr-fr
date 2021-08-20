---
title: Exigences liées aux schémas de données
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340825"
---
Metrics Advisor est un service conçu pour la détection, le diagnostic et l’analyse des anomalies dans les séries chronologiques. En tant que service basé sur l’intelligence artificielle, il utilise vos données pour entraîner le modèle utilisé. Ce service accepte des tables de données agrégées avec les colonnes suivantes :

* **Mesure** (obligatoire) : Une mesure est un terme fondamental ou spécifique à une unité et une valeur quantifiable de la métrique. Elle désigne une ou plusieurs colonnes contenant des valeurs numériques.
* **Horodatage** (facultatif) : zéro ou une colonne de type `DateTime` ou `String`. Lorsque cette colonne n’est pas définie, l’horodatage est défini comme heure de début de chaque période d’ingestion. Mettez l’horodatage sous la forme : `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimension** (obligatoire) : Une dimension désigne une ou plusieurs valeurs catégoriques. Une combinaison de ces valeurs identifie une série chronologique particulière à une seule dimension, par exemple : pays, langue, abonné, etc. Les colonnes de la dimension peuvent être de n’importe quel type de données. Soyez prudent lorsque vous travaillez avec de grands volumes de colonnes et de valeurs, afin d’éviter le traitement d’un nombre excessif de dimensions.

Voici un exemple de schéma de métriques attendu : 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

Agrégez vos données à l’avance pour vous aligner sur le schéma de métriques attendu si vous utilisez des sources de données comme ADLS, Azure Blob et autres, qui utilisent un fichier comme entrée de métriques. Toutefois, si vous utilisez des sources de données comme Azure SQL Server, Azure Data Explorer ou d’autres sources, qui prennent en charge l’exécution d’une requête pour obtenir des données de métriques à partir des sources, vous pouvez utiliser des fonctions d’agrégation pour agréger les données dans le schéma attendu.
