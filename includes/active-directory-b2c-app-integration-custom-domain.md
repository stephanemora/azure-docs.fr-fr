---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 33b42af7804c2c48a0112e0f8eb502c253813f47
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037091"
---
## <a name="use-a-custom-domain"></a>Utiliser un domaine personnalisé

En utilisant un [domaine personnalisé](../articles/active-directory-b2c/custom-domain.md), vous pouvez intégralement marquer l’URL d’authentification. En ce qui concerne les utilisateurs, ils restent sur votre domaine pendant le processus d’authentification, au lieu d’être redirigés vers le nom de domaine Azure AD B2C b2clogin.com.

Pour supprimer toutes les références à « b2c » dans l’URL, vous pouvez remplacer le nom de votre locataire b2c (contoso.onmicrosoft.com) dans l’URL de la requête d’authentification par le GUID d’ID de votre locataire. Par exemple, vous pouvez remplacer `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`.
