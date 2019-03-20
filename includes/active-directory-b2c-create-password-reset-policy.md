---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114011"
---
Pour activer la réinitialisation de mot de passe affinée sur votre application, vous utilisez un flux utilisateur de **réinitialisation de mot de passe**. Notez que l’option de réinitialisation du mot de passe au niveau du client est spécifiée [ici](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Ce flux utilisateur décrit les expériences clients lors de la réinitialisation de mot de passe, ainsi que le contenu des jetons que l’application reçoit en cas d’opération réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sous **Gérer**, sélectionnez **Flux utilisateur** et cliquez sur +**Nouveau flux utilisateur**.

![Sélectionnez Nouveau flux utilisateur](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Sous l’onglet **Recommandé**, sélectionnez **Réinitialisation du mot de passe**.

Entrez un **Nom** de flux utilisateur servant de référence pour votre application. Par exemple, entrez : `SSPR`.

Sous **Fournisseurs d’identité**, sélectionnez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.

![Entrez un nom et sélectionnez la réinitialisation du mot de passe à l’aide de l’adresse e-mail en tant que fournisseur d’identité](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Sous **Revendications d’application**, cliquez sur **Afficher plus** et choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience de réinitialisation du mot de passe réussie. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.

Cliquez sur **OK**.

![Sélectionnez des revendications d’application et cliquez sur le bouton OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Cliquez sur **Créer** pour ajouter le flux utilisateur. Le flux utilisateur est répertorié en tant que **B2C_1_SSPR**. Le préfixe **B2C_1_** est ajouté au nom.

Cliquez sur **Exécuter le flux utilisateur**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter le flux utilisateur**.

![Sélectionnez le flux utilisateur et exécutez-le](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Application** | Application de Contoso B2C |
| **Sélectionner l’URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vérifier l’expérience du client consistant à réinitialiser un mot de passe dans votre application.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>
