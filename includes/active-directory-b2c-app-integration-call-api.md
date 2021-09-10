---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227727"
---
Une fois l’authentification terminée, les utilisateurs interagissent avec l’application, qui appelle une API web protégée. L’API web utilise l’authentification par [jeton du porteur](https://datatracker.ietf.org/doc/html/rfc6750). Le jeton du porteur est le jeton d’accès obtenu par l’application auprès d’Azure AD B2C. L’application transmet le jeton dans l’en-tête d’autorisation de la requête HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si l’étendue du jeton d’accès ne correspond pas aux étendues de l’API web, la bibliothèque d’authentification obtient un nouveau jeton d’accès avec les étendues appropriées.
