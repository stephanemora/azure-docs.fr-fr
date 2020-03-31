---
title: Fichier Include
description: Fichier Include
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279547"
---
Dans la section **Gérer** (menu situé en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **exemple de requête**, puis collez-le dans un nouvel onglet de navigateur.

L’URL du point de terminaison ressemble au format suivant, avec votre propre ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```