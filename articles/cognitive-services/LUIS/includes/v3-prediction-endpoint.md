---
title: Comment obtenir un point de terminaison de prédiction V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589126"
---
1. Dans le portail LUIS, dans la section **Gérer** (menu en haut à droite), dans la page **Ressources Azure** (menu de gauche), sous l’onglet **Ressources de prédiction**, copiez l’exemple de requête **Example Query** en bas de la page.

    Collez l’URL dans un nouvel onglet de navigateur.

    L’URL contient votre ID d’application, votre clé et le nom de l’emplacement. L’URL du point de terminaison de prédiction V3 ressemble à ce qui suit :

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

