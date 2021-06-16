---
title: Exigences liées aux requêtes
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: c7d038153385c2d36d48f660a9a4e2bab29a3c45
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111893993"
---
Dans la requête, utilisez le paramètre `@StartTime` afin d’obtenir des données de métrique pour un horodatage. Metrics Advisor remplace le paramètre par une chaîne de format `yyyy-MM-ddTHH:mm:ss` lors de l’exécution de la requête.

> [!IMPORTANT]
> La requête doit retourner au plus un enregistrement pour chaque combinaison de dimensions, à chaque horodatage. Et tous les enregistrements retournés par la requête doivent avoir les mêmes horodatages. Metrics Advisor exécutera cette requête pour chaque horodatage afin d’ingérer vos données. Pour plus d’informations et des exemples, consultez la [Section FAQ sur les requêtes](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 