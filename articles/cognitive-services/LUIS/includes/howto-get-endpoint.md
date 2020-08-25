---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375639"
---
Dans la section **Gérer** (menu en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **Exemple de requête**, puis collez-le dans un nouvel onglet de navigateur.

L’URL du point de terminaison ressemble au format suivant, avec votre propre sous-domaine personnalisé, l’ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```