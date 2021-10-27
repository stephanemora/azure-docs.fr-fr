---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: ca25f04da2f626c55eb167bec6622f51780dc68c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069562"
---
L’API et les formats d’URI d’ID d’application basée sur le schéma HTTP suivants sont pris en charge. Remplacez les valeurs d’espace réservé comme décrit dans la liste qui suit le tableau.

| ID d’application pris en charge <br/> Formats d’URI | Exemples d’URI d’ID d’application |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _https://contoso.onmicrosoft.com/productsapi_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _https://contoso.onmicrosoft.com/productsapi_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _https://product.contoso.onmicrosoft.com_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _https://product.onmicrosoft.com/productsapi_   |


- _\<appId\>_ - Propriété de l’identificateur d’application (appId) de l’objet d’application.
- _\<string\>_ - Valeur de chaîne pour l’hôte ou le segment de chemin d’accès de l’API.
- _\<tenantId\>_ - Chaque locataire Azure Azure AD est fourni avec deux domaines initiaux sous la forme _\<tenantId\>.onmicrosoft.com_, où _\<tenantID\>_ est le nom de domaine initial que le créateur du locataire a spécifié lors de la création du locataire et un GUID généré par Azure pour représenter le locataire dans azure.
- _\<verifiedCustomDomain\>_ - [Domaine personnalisé vérifié](../articles/active-directory/fundamentals/add-custom-domain.md) configuré pour votre locataire Azure AD.

