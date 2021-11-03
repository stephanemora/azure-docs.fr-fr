---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 9de7912b426d659a03e8a7653c690cb16f83010e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007598"
---
Une fois l’authentification terminée, les utilisateurs interagissent avec l’application, qui appelle une API web protégée. L’API web utilise l’authentification par [jeton du porteur](https://datatracker.ietf.org/doc/html/rfc6750). Le jeton du porteur est le jeton d’accès obtenu par l’application auprès d’Azure AD B2C. L’application transmet le jeton dans l’en-tête d’autorisation de la requête HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si l’étendue du jeton d’accès ne correspond pas aux étendues de l’API web, la bibliothèque d’authentification obtient un nouveau jeton d’accès avec les étendues appropriées.
