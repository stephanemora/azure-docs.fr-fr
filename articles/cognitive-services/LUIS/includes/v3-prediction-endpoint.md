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
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287745"
---
1. Dans le portail LUIS, dans la section **Manage** (Gérer) (menu en haut à droite), dans la page **Azure Resources** (Ressources Azure) (menu de gauche), sous l’onglet **Prediction Resources** (Ressources de prédiction), copiez l’exemple de requête (**example Query**) en bas de la page.

    Collez l’URL dans un nouvel onglet de navigateur.

    L’URL contient votre ID d’application, votre clé et le nom de l’emplacement. L’URL du point de terminaison de prédiction V3 ressemble à ce qui suit :

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

