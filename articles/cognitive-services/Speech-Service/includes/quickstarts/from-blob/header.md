---
title: 'Démarrage rapide : Reconnaître la voix stockée dans le stockage Blob – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828907"
---
Dans ce guide de démarrage rapide, vous allez utiliser l’[API REST Batch Transcription](../../../batch-transcription.md) pour reconnaître la voix stockée dans un [objet blob SAS](https://aka.ms/ignite2019/speech/placeholder). Une fois les prérequis respectés, la reconnaissance vocale à l’aide de l’API REST ne comprend que quelques étapes :
> [!div class="checklist"]
> * Envoyer la requête JSON au service Speech pour commencer la transcription
> * Vérifier l’état de la transcription
> * Télécharger les résultats de la transcription une fois celle-ci terminée