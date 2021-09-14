---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 8547dac26c8d3a6a286a49a6b6e12919199a994b
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220340"
---
## <a name="use-a-custom-domain"></a>Utiliser un domaine personnalisé

En utilisant un [domaine personnalisé](../articles/active-directory-b2c/custom-domain.md), vous pouvez intégralement marquer l’URL d’authentification. En ce qui concerne les utilisateurs, ils restent sur votre domaine pendant le processus d’authentification, au lieu d’être redirigés vers le nom de domaine Azure AD B2C b2clogin.com.

Pour supprimer toutes les références à « b2c » dans l’URL, vous pouvez remplacer le nom de votre locataire b2c (contoso.onmicrosoft.com) dans l’URL de la requête d’authentification par le GUID d’ID de votre locataire. Par exemple, vous pouvez remplacer `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`.
