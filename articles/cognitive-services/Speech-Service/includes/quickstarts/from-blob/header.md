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
ms.date: 12/17/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 495d2a8e38b58eb3ef1494e6a3a33ee1dc32d049
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466573"
---
Dans ce guide de démarrage rapide, vous allez utiliser une API REST pour reconnaître la voix dans des fichiers dans un processus de traitement par lots. Un processus de traitement par lots exécute la transcription vocale sans aucune interaction de l’utilisateur. Il offre un modèle de programmation simple, sans qu’il soit nécessaire de gérer la concurrence, des modèles vocaux personnalisés ou d’autres détails. Cela implique des options de contrôle avancées ainsi qu’une utilisation efficace des ressources du service Azure Speech.

La [vue d’ensemble de la transcription par lot](../../../batch-transcription.md) décrit en détail l’utilisation de cette fonctionnalité. L’API détaillée est disponible sous forme de [document Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A) intitulé `Custom Speech transcriptins`. 

Le guide de démarrage rapide suivant vous présente un exemple d’utilisation pas à pas.
