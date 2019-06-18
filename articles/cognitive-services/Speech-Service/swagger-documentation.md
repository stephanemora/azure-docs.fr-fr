---
title: Documentation Swagger - Services Speech
titleSuffix: Azure Cognitive Services
description: La documentation Swagger peut être utilisée pour générer automatiquement des SDK pour plusieurs langages de programmation. Toutes les opérations de notre service sont prises en charge par Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743217"
---
# <a name="swagger-documentation"></a>Documentation Swagger

Les services Speech offre une spécification Swagger pour interagir avec quelques API REST utilisées pour importer des données, créer des modèles, tester la précision des modèles, créer des points de terminaison personnalisés, mettre en file d’attente des transcriptions par lots et gérer des abonnements. La plupart des opérations disponibles par le biais du portail Custom Speech peuvent être effectuées par programmation à l’aide de ces API. 

> [!NOTE]
> Les opérations de reconnaissance vocale et de synthèse vocale sont prises en charge et disponibles en tant qu’API REST, à leur tour décrites dans la spécification Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Génération de code à partir de la spécification Swagger

La [spécification Swagger](https://cris.ai/swagger/ui/index) comporte des options qui vous permettent de tester rapidement divers parcours. En revanche, il est parfois souhaitable de générer du code pour tous les parcours, en créant une bibliothèque unique d’appels sur laquelle vous pouvez baser les futures solutions. Intéressons-nous au processus permettant de générer une bibliothèque Python.

Vous avez besoin de définir Swagger sur la même région que celle de votre abonnement aux services Speech. Vous pouvez vérifier votre région dans le portail Azure sous votre ressource Services Speech. Pour obtenir la liste complète des régions prises en charge, consultez [Régions](regions.md).

1. Accédez à https://editor.swagger.io
2. Cliquez sur **Fichier**, puis sur **Importer**
3. Entrez l’URL de Swagger, en incluant la région de votre abonnement aux services Speech `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Cliquez sur **Générer un client** et sélectionnez Python
5. Obtenez la bibliothèque de client

Vous pouvez utiliser la bibliothèque Python que vous avez générée avec les [exemples de services Speech sur GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documents de référence

* [REST (Swagger) : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Étapes suivantes

* [Exemples de services Speech sur GitHub](https://aka.ms/csspeech/samples)
* [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
