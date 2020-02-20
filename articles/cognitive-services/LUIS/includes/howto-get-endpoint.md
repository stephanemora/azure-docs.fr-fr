---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279547"
---
Dans la section **Gérer** (menu situé en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **exemple de requête**, puis collez-le dans un nouvel onglet de navigateur.

L’URL du point de terminaison ressemble au format suivant, avec votre propre ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```