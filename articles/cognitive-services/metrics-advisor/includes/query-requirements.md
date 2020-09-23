---
title: Exigences liées aux requêtes
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930667"
---
Dans la requête, utilisez le paramètre `@StartTime` afin d’obtenir des données de métrique pour un horodatage particulier. Il sera remplacé par une chaîne au format `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Assurez-vous que seules les données de métrique issues d’**un seul horodatage** seront retournées par la requête. Metrics Advisor exécutera la requête sur chaque horodatage pour obtenir les données de métrique correspondantes. Par exemple, une requête pour une métrique avec une fréquence *quotidienne* ne doit contenir qu’un seul horodatage, tel que `2020-06-21T00:00:00Z` lors de l’exécution de la requête une seule fois. 
