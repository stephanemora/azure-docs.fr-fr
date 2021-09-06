---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524057"
---
Une fois l’authentification terminée, les utilisateurs interagissent avec l’application, qui appelle une API web protégée. L’API web utilise l’authentification par [jeton du porteur](https://datatracker.ietf.org/doc/html/rfc6750). Le jeton du porteur est le jeton d’accès obtenu par l’application auprès d’Azure AD B2C. L’application transmet le jeton dans l’en-tête d’autorisation de la requête HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si l’étendue du jeton d’accès ne correspond pas aux étendues de l’API web, la bibliothèque d’authentification obtient un nouveau jeton d’accès avec les étendues appropriées.
