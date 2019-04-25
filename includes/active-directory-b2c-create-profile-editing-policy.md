---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456034"
---
Si vous souhaitez activer la modification de profil dans votre application, vous utilisez un flux utilisateur de **modification de profil**. Ce flux utilisateur décrit les expériences clients lors de la modification du profil, ainsi que le contenu des jetons que l’application reçoit en cas d’opération réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sous **Gérer**, sélectionnez **Flux utilisateur** et cliquez sur +**Nouveau flux utilisateur**.

![Sélectionnez Nouveau flux utilisateur](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Sous l’onglet **Recommandé**, sélectionnez **Modification de profil**.

Entrez un **Nom** de flux utilisateur servant de référence pour votre application. Par exemple, entrez : `SiPe`.

Sous **Fournisseurs d’identité**, cochez **Connexion du compte local**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés.

![Sélectionnez la Connexion du compte local comme fournisseur d’identité et cliquez sur le bouton OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

Sous **Attributs utilisateur**, cliquez sur **Afficher plus**. Dans la colonne **Collecter l'attribut**, choisissez les attributs que le client peut afficher et modifier sur son profil. Par exemple, vérifiez **Pays/région**, **Nom d’affichage** et **Code postal**.

Dans la colonne **Revendication de retour**, choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience de modification de profil réussie. Par exemple, sélectionnez **Nom d’affichage** et **Code postal**.

Cliquez sur **OK**.

![Sélectionnez des revendications d’application et cliquez sur le bouton OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Cliquez sur **Créer** pour ajouter le flux utilisateur. Le flux utilisateur est répertorié en tant que **B2C_1_SiPe**. Le préfixe **B2C_1_** est ajouté au nom.

Sélectionnez **Exécuter le flux utilisateur**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter le flux utilisateur**.

![Sélectionnez le flux utilisateur et exécutez-le](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Application** | Application de Contoso B2C |
| **URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vérifier l’expérience du client consistant à modifier un profil telle qu’elle a été configurée.

> [!NOTE]
> La création du flux utilisateur et les mises à jour peuvent prendre jusqu’à une minute.
>

