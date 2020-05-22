---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588778"
---
Dans la section **Gérer** (menu en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **Exemple de requête**, puis collez-le dans un nouvel onglet de navigateur.

L’URL du point de terminaison ressemble au format suivant, avec votre propre sous-domaine personnalisé, l’ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```