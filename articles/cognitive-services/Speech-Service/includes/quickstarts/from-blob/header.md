---
title: 'Démarrage rapide : Reconnaître la voix stockée dans le stockage Blob - Service Speech'
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
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506098"
---
Dans ce guide de démarrage rapide, vous allez utiliser l’[API REST Batch Transcription](../../../batch-transcription.md) pour reconnaître la voix stockée dans un [objet blob SAS](https://aka.ms/ignite2019/speech/placeholder). Une fois les prérequis respectés, la reconnaissance vocale à l’aide de l’API REST ne comprend que quelques étapes :
> [!div class="checklist"]
> * Envoyer la requête JSON au service Speech pour commencer la transcription
> * Vérifier l’état de la transcription
> * Télécharger les résultats de la transcription une fois celle-ci terminée