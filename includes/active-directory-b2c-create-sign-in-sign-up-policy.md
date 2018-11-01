---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 3485cc96ad1d9ab8f89facf33687a7ab2be43b1e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134377"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Dans la section stratégie des paramètres, sélectionnez **Stratégies d’inscription ou de connexion** et cliquez sur **+ Ajouter**.

![Sélectionnez des stratégies d’inscription ou de connexion et cliquez sur Ajouter.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Entrez un **Nom** de stratégie servant de référence pour votre application. Par exemple, entrez : `SiUpIn`.

Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Inscription par e-mail**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.

![Sélectionnez l’inscription par e-mail comme fournisseur d’identité et cliquez sur le bouton OK.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Sélectionnez **Attributs d’inscription**. Choisissez les attributs que vous souhaitez collecter auprès du client au cours de l’inscription. Par exemple, vérifiez **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

![Sélectionnez certains attributs et cliquez sur le bouton OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Cliquez sur **Revendications de l’application**. Choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience de d’inscription ou de connexion réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal**, **Nouvel utilisateur** et **ID d’objet de l’utilisateur**.

![Sélectionnez des revendications d’application et cliquez sur le bouton OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Cliquez sur **Créer** pour ajouter la stratégie. La stratégie est répertoriée en tant que **B2C_1_SiUpIn**. Le préfixe **B2C_1_** est ajouté au nom.

Ouvrez la stratégie en sélectionnant **B2C_1_SiUpIn**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter maintenant**.

![Sélectionner la stratégie et l’exécuter](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Applications** | Application de Contoso B2C |
| **Sélectionner l’URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire ou à vous connecter telle qu’elle a été configurée.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>