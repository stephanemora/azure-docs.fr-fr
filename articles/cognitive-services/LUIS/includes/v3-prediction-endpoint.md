---
title: Comment obtenir un point de terminaison de prédiction V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495257"
---
1. Dans le portail LUIS, dans la section Gérer (menu en haut à droite), dans la page Clés et points de terminaison (menu gauche), sélectionnez l’URL de point de terminaison en bas de la page.

    Cette action ouvre un onglet de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresse.

    L’URL contient votre ID d’application, votre clé et le nom de l’emplacement. L’URL du point de terminaison de prédiction V3 ressemble à ce qui suit :

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

