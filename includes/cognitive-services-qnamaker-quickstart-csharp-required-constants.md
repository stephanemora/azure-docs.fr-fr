---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 523ce8a709c0ea4be361b92c65a61ed017f0ccc7
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019097"
---
En haut de la classe Program, ajoutez l’instruction suivante pour accéder à QnA Maker :

```csharp
// Represents the various elements used to create HTTP request URIs
// for QnA Maker operations.
static string host = "https://westus.api.cognitive.microsoft.com";
static string service = "/qnamaker/v4.0";
static string method = "/knowledgebases/create";

// NOTE: Replace this value with a valid QnA Maker subscription key.
static string key = "<your-qna-maker-subscription-key>";
```

Les trois premières constantes sont utilisées pour créer l’URL complète de l’API. La dernière constante fournit une authentification à l’API. 