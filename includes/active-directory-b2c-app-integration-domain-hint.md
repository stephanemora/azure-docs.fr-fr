---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: bade30eb42235e65170fc59ad77fa2f4ce1114c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801663"
---
## <a name="preselect-an-identity-provider"></a>Présélectionner un fournisseur d'identité

Si vous avez configuré la procédure de connexion pour votre application afin d’inclure des comptes de réseaux sociaux comme Facebook, LinkedIn ou Google, vous pouvez spécifier le paramètre `domain_hint`. Ce paramètre de requête fournit un indicateur à Azure AD B2C concernant le fournisseur d’identité sociale qui doit être utilisé pour la connexion. Par exemple, si l’application spécifie `domain_hint=facebook.com`, le flux de connexion accède directement à la page de connexion Facebook. 

Pour rediriger les utilisateurs vers un fournisseur d’identité externe, procédez comme suit :
