---
title: Comment obtenir un point de terminaison de prédiction V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544962"
---
1. Dans le portail LUIS, dans la section **Gérer** (menu en haut à droite), dans la page **Ressources Azure** (menu de gauche), sous l’onglet **Ressources de prédiction**, copiez l’exemple de requête **Example Query** en bas de la page.

    Collez l’URL dans un nouvel onglet de navigateur.

    L’URL contient votre ID d’application, votre clé et le nom de l’emplacement. L’URL du point de terminaison de prédiction V3 ressemble à ce qui suit :

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

