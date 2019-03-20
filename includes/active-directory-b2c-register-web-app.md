---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "58115276"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Pour inscrire votre application web, utilisez les paramètres spécifiés dans la table.

![Exemples de paramètres d’inscription pour les nouvelles applications web](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Paramètre      | Exemple de valeur  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nom** | Application de Contoso B2C | Entrez un **Nom** pour l’application qui décrira votre application aux consommateurs. | 
| **Inclure une application/API web** | Oui | Sélectionnez **Oui** pour une application web. |
| **Autoriser le flux implicite** | Oui | Choisissez **Oui** si votre application doit utiliser une [connexion OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL de réponse** | `https://localhost:44316` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Entrez une URL [appropriée](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) comme **URL de réponse**. Dans cet exemple, votre application web est locale et à l’écoute sur le port 44316. |

Cliquez sur **Créer** pour inscrire votre application.

Votre application qui vient d’être enregistrée s’affiche dans la liste des applications du client B2C. Sélectionnez votre application web dans la liste. Le volet de propriétés de l’application web s’affiche.

![Propriétés de l’application web](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Prenez note de l’**ID client de l’application** global unique. Vous utilisez l’ID dans le code de votre application.