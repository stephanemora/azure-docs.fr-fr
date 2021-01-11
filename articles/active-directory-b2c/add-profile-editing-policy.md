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
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1cf66918d8b5211dcc03be944dbcb92cdf6e0773
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618596"
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
1. Pour **Fournisseurs d’identité**, sélectionnez **Connexion par e-mail**.
1. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Nom d’affichage** et **Poste**. Cliquez sur **OK**.
1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
1. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et la stratégie de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* Ajoutez une [connexion avec un fournisseur d’identité sociale](add-identity-provider.md).