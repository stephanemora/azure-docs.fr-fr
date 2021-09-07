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
ms.openlocfilehash: 32fc672bf41c35881baf082b5694d1800084889d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745962"
---
Azure Metrics Advisor est un service de détection, de diagnostic et d’analyse d’anomalies dans les séries chronologiques. En tant que service basé sur l’IA, il tire parti de vos données pour entraîner le modèle utilisé. Ce service accepte des tables de données agrégées avec les colonnes suivantes :

* **Mesure** (obligatoire) : Une mesure est un terme fondamental ou spécifique à une unité et une valeur quantifiable de la métrique. Elle désigne une ou plusieurs colonnes contenant des valeurs numériques.
* **Horodatage** (facultatif) : zéro ou une colonne de type `DateTime` ou `String`. Quand cette colonne n’est pas définie, l’horodatage est défini en tant qu’heure de début de chaque période d’ingestion. Mettez en forme l’horodatage de la manière suivante : `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimension** (obligatoire) : Une dimension désigne une ou plusieurs valeurs catégoriques. La combinaison de ces valeurs identifie une série chronologique univariée particulière (par exemple le pays, la langue et le locataire). Les colonnes de la dimension peuvent être de n’importe quel type de données. Soyez prudent lorsque vous travaillez avec de grands volumes de colonnes et de valeurs, afin d’éviter le traitement d’un nombre excessif de dimensions.

Si vous utilisez des sources de données telles qu’Azure Data Lake Storage ou Stockage Blob Azure, vous pouvez agréger vos données pour les aligner sur le schéma de métriques attendu. En effet, ces sources de données utilisent un fichier en tant qu’entrée de métriques.

Si vous utilisez des sources de données telles qu’Azure SQL ou Azure Data Explorer, vous pouvez tirer parti des fonctions d’agrégation pour agréger les données dans le schéma attendu. En effet, ces sources de données prennent en charge l’exécution d’une requête permettant d’obtenir des données de métriques à partir de sources.

