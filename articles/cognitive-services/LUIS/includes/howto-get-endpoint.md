---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648064"
---
Dans la section **Gérer** (menu situé en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **exemple de requête**, puis collez-le dans un nouvel onglet de navigateur. 

L’URL du point de terminaison ressemble au format suivant, avec votre propre ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```