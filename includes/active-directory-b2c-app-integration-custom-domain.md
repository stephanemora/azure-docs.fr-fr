---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803446"
---
## <a name="use-a-custom-domain"></a>Utiliser un domaine personnalisé

En utilisant un [domaine personnalisé](../articles/active-directory-b2c/custom-domain.md), vous pouvez intégralement marquer l’URL d’authentification. Du point de vue de l’utilisateur, l’utilisateur reste sur votre domaine pendant le processus d’authentification, au lieu d’être redirigé vers le nom de domaine Azure AD B2C b2clogin.com.

Vous pouvez remplacer le nom de votre locataire B2C (contoso.onmicrosoft.com) dans l’URL de la requête d’authentification par le GUID d’ID de votre locataire pour supprimer toutes les références à « b2c » dans l’URL. Par exemple, vous pouvez remplacer `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`.
