---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114317"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sous **Gérer**, sélectionnez **Flux utilisateur** et cliquez sur +**Nouveau flux utilisateur**.

![Sélectionnez Nouveau flux utilisateur](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Sous l’onglet **Recommandé**, sélectionnez **Inscription et connexion**.

![Sélectionnez Inscription et connexion du flux utilisateur](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Entrez un **Nom** de flux utilisateur servant de référence pour votre application. Par exemple, entrez : `SiUpIn`.

Sous **Fournisseurs d’identité**, cochez **Inscription par e-mail**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés.

Sous **Authentification multifacteur**, choisissez **Activé** ou **Désactivé**.

![Entrez un nom et sélectionnez l’inscription par e-mail comme fournisseur d’identité](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

Sous **Attributs utilisateur et revendications**, sélectionnez **Afficher plus** pour afficher la liste complète des attributs et des revendications disponibles.

Dans la colonne **Collecter l'attribut**, choisissez les attributs que vous souhaitez collecter auprès du consommateur lors de l’inscription. Par exemple, vérifiez **Pays/région**, **Nom d’affichage** et **Code postal**.

Dans la colonne **Revendication de retour**, choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience d’inscription ou de connexion réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal**, **Nouvel utilisateur** et **ID d’objet de l’utilisateur**.

Cliquez sur **OK**.

![Sélectionnez des attributs utilisateur et des revendications et cliquez sur le bouton OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Cliquez sur **Créer** pour ajouter le flux utilisateur. Le flux utilisateur est répertorié en tant que **B2C_1_SiUpIn**. Le préfixe **B2C_1_** est ajouté au nom.

Sélectionnez **Exécuter le flux utilisateur**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter le flux utilisateur**.

![Sélectionnez Exécuter le flux utilisateur](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Application** | Application de Contoso B2C |
| **URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire ou à vous connecter telle qu’elle a été configurée.

> [!NOTE]
> La création du flux utilisateur et les mises à jour peuvent prendre jusqu’à une minute.
>