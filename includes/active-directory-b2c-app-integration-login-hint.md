---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: c2409ef7d846ff7d4d67d7bf79ab96760375da81
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037051"
---
## <a name="prepopulate-the-sign-in-name"></a>Préremplir le nom de connexion

Pendant le parcours utilisateur pour la connexion, votre application peut cibler un utilisateur spécifique. Quand une application cible un utilisateur, celle-ci peut spécifier, dans la requête d’autorisation, le paramètre de requête `login_hint` avec le nom de connexion de l’utilisateur. Azure AD B2C remplit automatiquement le nom de connexion, et l’utilisateur n’a que le mot de passe à fournir. 

Pour préremplir le nom de connexion, procédez comme suit :