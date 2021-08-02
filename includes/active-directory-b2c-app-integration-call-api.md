---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: fe128e7eee99d2b396c758979f0efff936ec6785
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072996"
---
Après s’être authentifié, l’utilisateur accède à une page qui appelle une API web protégée avec une authentification par jeton du porteur. Le jeton du porteur est le jeton d’accès obtenu auprès d’Azure AD B2C. L’application transmet le jeton dans l’en-tête d’autorisation de la requête HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si l’étendue du jeton d’accès ne correspond pas aux étendues de l’API web, la bibliothèque d’authentification obtient un nouveau jeton d’accès avec les étendues appropriées.
s