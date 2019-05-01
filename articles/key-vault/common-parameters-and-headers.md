---
title: Paramètres et en-têtes communs
description: Paramètres et en-têtes communs à toutes les opérations que vous pouvez effectuer concernant les ressources Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d635c7bdc6602c662ea6b91aad7e3f7a5e726547
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696685"
---
# <a name="common-parameters-and-headers"></a>Paramètres et en-têtes communs

Les informations suivantes sont communes à toutes les opérations que vous pouvez effectuer concernant les ressources Key Vault :

- Remplacez `{api-version}` par la version de l’API dans l’URI.
- Remplacez `{subscription-id}` par l’identificateur de votre abonnement dans l’URI
- Remplacez `{resource-group-name}` par le groupe de ressources. Pour plus d’informations, consultez la rubrique Utilisation des groupes de ressources pour gérer vos ressources Azure.
- Remplacez `{vault-name}` par le nom de votre coffre de clés dans l’URI.
- Définissez l’en-tête Content-Type sur application/json.
- Définissez l’en-tête d’autorisation sur un JSON Web Token que vous avez obtenu auprès d’Azure Active Directory (AAD). Pour plus d’informations, consultez [Demandes d’authentification Azure Resource Manager](authentication-requests-and-responses.md).

## <a name="common-error-response"></a>Réponse d’erreur commune
Le service utilise les codes d’état HTTP pour indiquer la réussite ou l’échec. En outre, les échecs contiennent une réponse au format suivant :

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nom de l'élément | Type | Description |
|---|---|---|
| code | string | Type d’erreur qui s’est produit.|
| Message | string | Description de ce qui a provoqué l’erreur. |



## <a name="see-also"></a>Voir aussi
 [Informations de référence sur l’API REST Azure Key Vault](/rest/api/keyvault/)
