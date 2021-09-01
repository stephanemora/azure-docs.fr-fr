---
title: Configurer un flux de modification du profil
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux de modification du profil dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f019fdc64ca30954017afc34267dcef998cf5fb8
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198833"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Configurer un flux de modification du profil dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Flux de modification du profil

La stratégie de modification de profil permet aux utilisateurs de gérer leurs attributs de profil, tels que le nom d’affichage, le nom, le prénom, la ville et autres informations. Le flux de modification de profil implique les étapes suivantes : 

1. Inscription ou connexion avec un compte local et ou un compte social. Si la session est toujours active, Azure AD B2C autorise l’utilisateur et passe à l’étape suivante.
1. Azure AD B2C lit le profil utilisateur dans l’annuaire et permet à l’utilisateur de modifier les attributs.

![Flux de modification du profil](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Prérequis

Si ce n’est pas déjà fait, [inscrivez une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Si vous voulez autoriser les utilisateurs à modifier leur profil dans votre application, vous utilisez un flux d’utilisateur de modification de profil.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Modifications de profil**. 
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *profileediting1*.
1. Sous **Fournisseurs d’identité**, sélectionnez au moins un fournisseur d’identité :

   * Sous **Comptes locaux**, sélectionnez l’une des options suivantes : **Connexion par e-mail**, **Connexion par ID utilisateur**, **Connexion par téléphone**, **Connexion par téléphone/e-mail**, **Connexion par ID utilisateur/e-mail**, ou **Aucun**. [Plus d’informations](sign-in-options.md)
   * Sous **Fournisseurs d’identité sociale**, sélectionnez un des fournisseurs d’identité sociale ou d’entreprise externes que vous avez configurés. [Plus d’informations](add-identity-provider.md)
1. Sous **Authentification multifacteur**, si vous voulez demander aux utilisateurs de vérifier leur identité avec une deuxième méthode d’authentification, choisissez le type de méthode et le moment auquel appliquer l’authentification multifacteur (MFA). [Plus d’informations](multi-factor-authentication.md)
1. Sous **Accès conditionnel**, si vous avez configuré des stratégies d’accès conditionnel pour votre locataire Azure AD B2C et que vous souhaitez les activer pour ce flux d’utilisateurs, cochez la case **Appliquer des stratégies d’accès conditionnel**. Vous n’avez pas besoin de spécifier un nom de stratégie. [Plus d’informations](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Pour la liste complète des valeurs, sélectionnez **Afficher plus**, choisissez les valeurs, puis sélectionnez **OK**.
1. Sélectionnez **Créer** pour ajouter le flux d’utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
1. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et la stratégie de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* Ajoutez une [connexion avec un fournisseur d’identité sociale](add-identity-provider.md).