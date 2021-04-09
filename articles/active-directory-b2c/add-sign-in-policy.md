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
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581862"
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
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Connexion**.
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Pour **Fournisseurs d’identité**, sélectionnez **Connexion par e-mail**.
1. Pour **Revendications de l’application**, choisissez les revendications et attributs que vous souhaitez renvoyer à votre application. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et revendications pour **Nom d’affichage**, **Prénom**, **Nom de famille** et **ID d’objet de l’utilisateur**. Cliquez sur **OK**.
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
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez le fichier de stratégie que vous avez modifié, *TrustFrameworkExtensions.xml*.
1. Sélectionnez la stratégie de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devez être en mesure de vous connecter avec le compte que vous avez créé (à l’aide de l’API MS Graph), sans le lien d’inscription.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* Ajoutez une [connexion avec un fournisseur d’identité sociale](add-identity-provider.md).
* Configurez un [flux de réinitialisation de mot de passe](add-password-reset-policy.md).
