---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 8363f7d13c2a991df031e3a2ca53efb8c1b11761
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037106"
---
## <a name="preselect-an-identity-provider"></a>Présélectionner un fournisseur d'identité

Si vous avez configuré la procédure de connexion pour votre application afin d’inclure des comptes de réseaux sociaux comme Facebook, LinkedIn ou Google, vous pouvez spécifier le paramètre `domain_hint`. Ce paramètre de requête fournit un indicateur à Azure AD B2C concernant le fournisseur d’identité sociale qui doit être utilisé pour la connexion. Par exemple, si l’application spécifie `domain_hint=facebook.com`, le flux de connexion accède directement à la page de connexion Facebook. 

Pour rediriger les utilisateurs vers un fournisseur d’identité externe, procédez comme suit :
