---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 9fb96a0427ecaa3431eddb47ca89458a6dcf5901
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801377"
---
## <a name="prepopulate-the-sign-in-name"></a>Préremplir le nom de connexion

Pendant le parcours utilisateur pour la connexion, votre application peut cibler un utilisateur spécifique. Quand une application cible un utilisateur, celle-ci peut spécifier, dans la demande d’autorisation, le `login_hint` paramètre de requête avec le nom de connexion de l’utilisateur. Azure AD B2C remplit automatiquement le nom de connexion, et l’utilisateur n’a que le mot de passe à fournir. 

Pour préremplir le nom de connexion, procédez comme suit :