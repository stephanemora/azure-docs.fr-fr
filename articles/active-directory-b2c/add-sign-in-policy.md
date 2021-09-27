---
title: Configurer un flux de connexion
titleSuffix: Azure Active Directory B2C
description: Découvrez comment configurer un de connexion dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 78556d5f6d6a203a3d105f971cb7850109daa01a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570271"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Configurer un flux de connexion dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Vue d’ensemble du flux de connexion

La stratégie d’inscription permet aux utilisateurs d’effectuer les actions suivantes : 

* Les utilisateurs peuvent se connecter avec un compte Azure AD B2C local
* Inscription ou connexion avec un compte social
* Réinitialisation du mot de passe
* Les utilisateurs ne peuvent pas s’inscrire pour un compte local Azure AD B2C. Pour créer un compte, un administrateur peut utiliser le [portail Azure](manage-users-portal.md#create-a-consumer-user)ou l’[API MS Graph](microsoft-graph-operations.md).

![Flux de modification du profil](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Prérequis

Si ce n’est pas déjà fait, [inscrivez une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Créer un flux d’utilisateur de connexion

Pour ajouter une stratégie de connexion :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Répertoires + Abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD B2C dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Connexion**.
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Sous **Fournisseurs d’identité**, sélectionnez au moins un fournisseur d’identité :

   * Sous **Comptes locaux**, sélectionnez l’une des options suivantes : **Connexion par e-mail**, **Connexion par ID utilisateur**, **Connexion par téléphone**, **Connexion par téléphone/e-mail**, **Connexion par ID utilisateur/e-mail**, ou **Aucun**. [Plus d’informations](sign-in-options.md)
   * Sous **Fournisseurs d’identité sociale**, sélectionnez l’un des fournisseurs d’identité sociale ou d’entreprise externes que vous avez configurés. [Plus d’informations](add-identity-provider.md)
1. Sous **Authentification multifacteur**, si vous voulez demander aux utilisateurs de vérifier leur identité avec une deuxième méthode d’authentification, choisissez le type de méthode et le moment auquel appliquer l’authentification multifacteur (MFA). [Plus d’informations](multi-factor-authentication.md)
1. Sous **Accès conditionnel**, si vous avez configuré des stratégies d’accès conditionnel pour votre locataire Azure AD B2C et que vous souhaitez les activer pour ce flux d’utilisateurs, cochez la case **Appliquer des stratégies d’accès conditionnel**. Vous n’avez pas besoin de spécifier un nom de stratégie. [Plus d’informations](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Sous **Revendications de l’application**, choisissez les revendications que vous souhaitez retourner à l’application dans le jeton. Pour la liste complète des valeurs, sélectionnez **Afficher plus**, choisissez les valeurs, puis sélectionnez **OK**.
   > [!NOTE]
   > Vous pouvez également [créer des attributs personnalisés](user-flow-custom-attributes.md?pivots=b2c-user-flow) afin de les utiliser dans votre locataire Azure AD B2C.
1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.
1. Vous devez être en mesure de vous connecter avec le compte que vous avez créé (à l’aide de l’API MS Graph), sans le lien d’inscription. Le jeton retourné comprend les revendications que vous avez sélectionnées.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Supprimer le lien d’inscription

Le profil technique **LocalAccountSignUpWithLogonEmail** est [autodéclaré](self-asserted-technical-profile.md). Il est appelé pendant le flux d’inscription ou de connexion. Pour supprimer le lien d’inscription, définissez les métadonnées `setting.showSignupLink` sur `false`. Remplacez les profils techniques SelfAsserted-LocalAccountSignin-Email dans le fichier d’extension. 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple _`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**_.
1. Recherchez l’élément `ClaimsProviders`. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez le fournisseur de revendications suivant à l’élément `ClaimsProviders` :

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. Dans l’élément `<BuildingBlocks>`, ajoutez la [ContentDefinition](contentdefinitions.md) suivante pour faire référence à la version 1.2.0 ou à une URI de données plus récent :

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Charger et tester votre stratégie

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD en sélectionnant l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez le fichier de stratégie que vous avez modifié, *TrustFrameworkExtensions.xml*.
1. Sélectionnez la stratégie de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devez être en mesure de vous connecter avec le compte que vous avez créé (à l’aide de l’API MS Graph), sans le lien d’inscription.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* Ajoutez une [connexion avec un fournisseur d’identité sociale](add-identity-provider.md).
* Configurez un [flux de réinitialisation de mot de passe](add-password-reset-policy.md).
