---
title: Documentation Swagger - Service Speech
titleSuffix: Azure Cognitive Services
description: La documentation Swagger peut être utilisée pour générer automatiquement des SDK pour plusieurs langages de programmation. Toutes les opérations de notre service sont prises en charge par Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430803"
---
# <a name="swagger-documentation"></a>Documentation Swagger

Le service Speech offre une spécification Swagger pour interagir avec quelques API REST utilisées pour importer des données, créer des modèles, tester le niveau de justesse des modèles, créer des points de terminaison personnalisés, mettre en file d’attente des transcriptions par lots et gérer des abonnements. La plupart des opérations disponibles par le biais du portail Custom Speech peuvent être effectuées par programmation à l’aide de ces API.

> [!NOTE]
> Les opérations de reconnaissance vocale et de synthèse vocale sont prises en charge et disponibles en tant qu’API REST, à leur tour décrites dans la spécification Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Génération de code à partir de la spécification Swagger

La [spécification Swagger](https://cris.ai/swagger/ui/index) comporte des options qui vous permettent de tester rapidement divers parcours. En revanche, il est parfois souhaitable de générer du code pour tous les parcours, en créant une bibliothèque unique d’appels sur laquelle vous pouvez baser les futures solutions. Intéressons-nous au processus permettant de générer une bibliothèque Python.

Vous avez besoin de définir Swagger sur la même région que celle de votre abonnement au service Speech. Vous pouvez vérifier votre région dans le portail Azure, sous votre ressource Speech. Pour obtenir la liste complète des régions prises en charge, consultez [Régions](regions.md).

1. Accédez à https://editor.swagger.io
2. Cliquez sur **Fichier**, puis sur **Importer**
3. Entrez l’URL de Swagger, en y incluant la région de votre abonnement au service Speech : `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Cliquez sur **Générer un client** et sélectionnez Python
5. Obtenez la bibliothèque de client

Vous pouvez utiliser la bibliothèque Python que vous avez générée avec les [exemples du service Speech sur GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documents de référence

* [REST (Swagger) : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Étapes suivantes

* [Exemples Speech sur GitHub](https://aka.ms/csspeech/samples)
* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
