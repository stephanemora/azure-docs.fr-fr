---
title: Paramètres et en-têtes communs
description: Paramètres et en-têtes communs à toutes les opérations que vous pouvez effectuer concernant les ressources Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: dae5e1ab6244d2898bc218ed5db3b6b2b90150cf
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294051"
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

   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  

|Nom de l'élément | type | Description |
|---|---|---|
| code | chaîne | Type d’erreur qui s’est produit.|
| Message | chaîne | Description de ce qui a provoqué l’erreur. |



## <a name="see-also"></a>Voir aussi
 [Informations de référence sur l’API REST Azure Key Vault](/rest/api/keyvault/)
