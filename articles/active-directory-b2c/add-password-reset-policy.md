---
title: Configuration d’un flux de réinitialisation de mot de passe
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux de réinitialisation de mot de passe dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618598"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configuration d’un flux de réinitialisation de mot de passe dans Azure Active Directory B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Flux de réinitialisation de mot de passe

La stratégie de réinitialisation de mot de passe permet aux utilisateurs de réinitialiser le mot de passe s’ils l’ont oublié. Le flux de réinitialisation du mot de passe implique les étapes suivantes : 
1. Dans la page d’inscription et de connexion, l’utilisateur clique sur le lien « vous avez oublié votre mot de passe ? » . Azure AD B2C retourne le code d’erreur AADB2C90118 à votre application. L’application gère ce code d’erreur en invoquant la stratégie de réinitialisation de mot de passe. 
1. Les utilisateurs fournissent leur courrier électronique et le confirment à l’aide d’un code secret à usage unique.
1. Entrez un nouveau mot de passe.

![Flux de réinitialisation de mot de passe](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Prérequis

Si ce n’est pas déjà fait, [inscrivez une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Pour permettre aux utilisateurs de votre application de réinitialiser leur mot de passe, utilisez un flux utilisateur de réinitialisation de mot de passe.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**. 
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *passwordreset1*.
1. Sous **Fournisseurs d’identité**, activez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.
2. Sous Revendications d’application, cliquez sur **Afficher plus** et choisissez les revendications à renvoyer à votre application dans les jetons d’autorisation. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.
3. Cliquez sur **OK**.
4. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et la stratégie de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Configurer un [flux de modification du profil](add-profile-editing-policy.md).