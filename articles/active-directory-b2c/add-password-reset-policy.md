---
title: Configuration d’un flux de réinitialisation de mot de passe
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux de réinitialisation de mot de passe dans le Répertoire actif Azure B2C (Azure AD B2C).
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
ms.openlocfilehash: d87a09615df4882b132fd407417c046d174e9a0f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570446"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configuration d’un flux de réinitialisation de mot de passe dans Azure Active Directory B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dans un [parcours d’inscription et de connexion](add-sign-up-and-sign-in-policy.md), les utilisateurs peuvent réinitialiser leurs mots de passe à l’aide du lien **Vous avez oublié votre mot de passe ?** . Ce flux de réinitialisation de mot de passe libre-service s’applique aux comptes locaux dans le Répertoire actif Azure B2C (Azure AD B2C) qui utilisent une [adresse de messagerie](sign-in-options.md#email-sign-in) ou un [nom d’utilisateur](sign-in-options.md#username-sign-in) avec un mot de passe pour la connexion.

Le flux de réinitialisation du mot de passe est constitué des étapes suivantes :

1. Sur la page d’inscription et de connexion, l’utilisateur sélectionne le lien **Vous avez oublié votre mot de passe ?** . Azure AD B2C lance le flux de réinitialisation du mot de passe.
1. Dans la boîte de dialogue suivante qui s’affiche, l’utilisateur entre son adresse de messagerie, puis sélectionne **Envoyer le code de vérification**. Azure AD B2C envoie un code de vérification à la boîte de réception de l’utilisateur. L’utilisateur copie ensuite le code de vérification de l’e-mail, le rentre sur la page de réinitialisation du mot de passe Azure AD B2C, puis sélectionne **Vérifier le code**.
1. L’utilisateur peut ensuite entrer un nouveau mot de passe. (Après la vérification de l’e-mail, l’utilisateur peut toujours appuyer sur le bouton **Modifier l’adresse e-mail** ; voir [Masquer le bouton de modification d’adresse e-mail](#hide-the-change-email-button).)

   ![Diagramme qui affiche trois boîtes de dialogue dans le flux de réinitialisation de mot de passe.](./media/add-password-reset-policy/password-reset-flow.png)

> [!TIP]
> Un utilisateur peut modifier son mot de passe à l’aide du processus de réinitialisation de mot de passe libre-service s’il oublie son mot de passe et souhaite le réinitialiser. Dans ce cas, choisissez l'une des options suivantes :
> - Pour prendre en charge les cas où un utilisateur connaît son mot de passe et souhaite le modifier, utilisez un [flux de modification de mot de passe](add-password-change-policy.md).
> - Si vous souhaitez forcer un utilisateur à réinitialiser son mot de passe (par exemple, lorsqu’il se connecte pour la première fois, lorsque ses mots de passe ont été réinitialisés par un administrateur, ou une fois qu’ils ont été migrés vers Azure AD B2C avec des mots de passe aléatoires), utilisez un flux de [réinitialisation de mot de passe forcé](force-password-reset.md) .

### <a name="hide-the-change-email-button"></a>Masquer le bouton de modification d’adresse e-mail

Une fois l’e-mail vérifié, l’utilisateur peut toujours sélectionner **Modifier l’adresse e-mail**, entrer une autre adresse e-mail, puis répéter la vérification par e-mail. Si vous préférez masquer le bouton **Modifier l’adresse e-mail**, vous pouvez modifier la CSS pour masquer les éléments HTML associés dans la boîte de dialogue. Par exemple, vous pouvez ajouter l’entrée CSS ci-dessous à selfAsserted.html et [personnaliser l’interface utilisateur avec des modèles html](customize-ui-with-html.md) :

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

Notez que le nom par défaut du bouton **Modifier l’adresse e-mail** sur *selfAsserted.html* est **changeclaims**. Pour rechercher le nom du bouton, sur la page d’inscription, examinez la source de la page à l’aide d’un outil de navigation comme Inspect.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Réinitialisation du mot de passe en libre-service (recommandé)

La nouvelle expérience de réinitialisation de mot de passe fait maintenant partie de la stratégie d’inscription ou de connexion. Quand l’utilisateur sélectionne le lien **Vous avez oublié votre mot de passe ?** , il est immédiatement dirigé vers l’expérience Mot de passe oublié. Votre application n’a plus besoin de gérer le [code d’erreur AADB2C90118](#password-reset-policy-legacy) et vous n’avez pas besoin d’une stratégie distincte pour la réinitialisation du mot de passe.

::: zone pivot="b2c-user-flow"

L’expérience de réinitialisation du mot de passe en libre-service peut être configurée pour les flux d’utilisateur de connexion (recommandé) ou d’inscription et de connexion (recommandé). Si vous n’avez pas l’un de ces flux d’utilisateurs configurés, créez un flux d’utilisateur d'[inscription ou de connexion](add-sign-up-and-sign-in-policy.md) .

Pour activer la réinitialisation du mot de passe en libre-service pour le flux d’utilisateur d’inscription ou de connexion :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Répertoires + Abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez un flux d’utilisateur d’inscription ou de connexion (de type **Recommandé**) que vous souhaitez personnaliser.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Configuration du mot de passe**, sélectionnez **Réinitialisation du mot de passe en libre-service**.
1. Sélectionnez **Enregistrer**.
1. Dans le menu de gauche, sous **Personnaliser**, sélectionnez **Mises en page**.
1. Dans **version de mise en page**, sélectionnez **2.1.3** ou une version ultérieure.
1. Sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Les sections suivantes décrivent comment ajouter une expérience de mot de passe libre-service à une stratégie personnalisée. L’exemple est basé sur les fichiers de stratégie inclus dans le [pack de démarrage sur les stratégies personnalisées](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack).

> [!TIP]
> Vous trouverez un exemple complet de la stratégie d’inscription et de connexion avec réinitialisation de mot de passe sur [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-that-a-user-selected-the-forgot-your-password-link"></a>Indiquer qu’un utilisateur a sélectionné le lien Vous avez oublié votre mot de passe ?

Pour indiquer à la stratégie que l’utilisateur a sélectionné le lien **Vous avez oublié votre mot de passe ?** , définissez une revendication booléenne. Utilisez la revendication pour diriger le parcours utilisateur vers le profil technique de **Mot de passe oublié**. La revendication peut également être émise à destination du jeton pour que l’application soit informée que l’utilisateur s’est connecté via le flux utilisateur Mot de passe oublié.

Déclarez vos revendications dans le [schéma de revendications](claimsschema.md). Ouvrez le fichier d’extensions de votre stratégie, par exemple, dans *SocialAndLocalAccounts/TrustFrameworkExtensions.xml*.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez la revendication suivante à l’élément **ClaimsSchema**.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="isForgotPassword">
          <DisplayName>isForgotPassword</DisplayName>
          <DataType>boolean</DataType>
          <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
        </ClaimType>
      <!--
      </ClaimsSchema>
    </BuildingBlocks> -->
    ```

### <a name="upgrade-the-page-layout-version"></a>Mettre à niveau la version de mise en page

La [version de mise en page](contentdefinitions.md#migrating-to-page-layout) 2.1.2 est requise pour activer le processus de réinitialisation du mot de passe libre-service dans le parcours d’inscription ou de connexion. Pour mettre à niveau la version de mise en page :

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ContentDefinitions](contentdefinitions.md). Si l’élément n’existe pas, ajoutez-le.
1. Modifiez l’élément **DataURI** dans l’élément **ContentDefinition** pour avoir l’ID `api.signuporsignin` :

    ```xml
    <!-- 
    <BuildingBlocks>
      <ContentDefinitions> -->
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
        </ContentDefinition>
      <!-- 
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

Un profil technique de transformation des revendications lance la revendication **isForgotPassword**. Le profil technique est référencé ultérieurement. Lorsqu’il est appelé, il définit la valeur de la revendication **isForgotPassword** sur `true` . Recherchez l’élément **ClaimsProviders**. Si l’élément n’existe pas, ajoutez-le. Ajoutez ensuite le fournisseur de revendications suivant :  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Le paramètre de profil technique **SelfAsserted-LocalAccountSignin-email** **.forgotPasswordLinkOverride** définit l’échange de revendications de réinitialisation de mot de passe qui s’exécute dans votre parcours utilisateur.

### <a name="add-the-password-reset-sub-journey"></a>Ajouter le sous-parcours de réinitialisation du mot de passe

L’utilisateur peut désormais se connecter, s’inscrire et effectuer une réinitialisation du mot de passe dans votre parcours utilisateur. Pour mieux organiser le parcours utilisateur, vous pouvez utiliser un [sous-parcours](subjourneys.md) afin de gérer le flux de réinitialisation du mot de passe.

Le sous-parcours est appelé à partir du parcours utilisateur et exécute les étapes spécifiques pour fournir l’expérience de réinitialisation du mot de passe à l’utilisateur. Utilisez le `Call` sous-parcours type afin que lorsque le sous-parcours est terminé, le contrôle est renvoyé à l’étape d’orchestration qui a initié le sous-parcours.

Recherchez l’élément **Sous-parcours**. Si l’élément n’existe pas, ajoutez-le après l’élément de **Parcours de l'utilisateur**. Ajoutez ensuite le sous-parcours suivant :

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Préparer votre parcours utilisateur

Ensuite, connectez le lien vous **avez oublié votre mot de passe ?** au sous-parcours du mot de passe oublié. Référencez l’ID de sous-parcours de mot de passe oublié dans l’élément **ClaimsProviderSelection** de l’étape **CombinedSignInAndSignUp** .

Si vous n’avez pas votre propre parcours utilisateur personnalisé avec une étape **CombinedSignInAndSignUp**, effectuez les étapes suivantes pour dupliquer un parcours utilisateur d’inscription ou de connexion existant. Sinon, passez à la section suivante.

1. Dans le pack de démarrage, ouvrez le fichier *TrustFrameworkBase.xml* .
1. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
1. Ouvrez *TrustFrameworkExtensions.xml* et recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
1. Créez un élément enfant de l’élément **UserJourneys** en collant tout le contenu de l’élément **UserJourney** que vous avez copié à l’étape 2.
1. Renommez l’ID du parcours utilisateur. Par exemple : `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Connectez le lien Vous avez oublié votre mot de passe ? au sous-parcours Mot de passe oublié

Dans votre parcours utilisateur, vous pouvez représenter le sous-parcours Mot de passe oublié en tant que **ClaimsProviderSelection**. L’ajout de cet élément permet de connecter le lien **Vous avez oublié votre mot de passe ?** au sous-parcours Mot de passe oublié.

1. Dans le parcours utilisateur, recherchez l’élément d’étape d’orchestration incluant `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"`. Il s’agit généralement de la première étape d’orchestration. L’élément **ClaimsProviderSelections** contient une liste de fournisseurs d’identité via lesquels un utilisateur peut se connecter. Ajoutez la ligne suivante :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. À la prochaine étape d’orchestration, ajoutez un élément **ClaimsExchange**. Ajoutez la ligne suivante :

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

1. Ajoutez l’étape d’orchestration suivante entre l’étape en cours et la suivante. La nouvelle étape d’orchestration que vous ajoutez vérifie si la revendication **isForgotPassword** existe. Si la revendication existe, elle appelle le [sous-parcours de réinitialisation de mot de passe](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```

1. Après avoir ajouté la nouvelle étape d’orchestration, renumérotez les étapes séquentiellement sans sauter d’entiers de 1 à N.

### <a name="set-the-user-journey-to-be-executed"></a>Définir le parcours utilisateur à exécuter

Maintenant que vous avez modifié ou créé un parcours utilisateur, dans la section **Partie de confiance**, spécifiez le parcours qu’Azure AD B2C exécutera pour cette stratégie personnalisée. Dans l’élément [RelyingParty](relyingparty.md), recherchez l’élément **DefaultUserJourney** . Mettez à jour l’élément **DefaultUserJourney ReferenceId** pour qu’il corresponde à l’ID du parcours utilisateur dans lequel vous avez ajouté **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Indiquer le flux Mot de passe oublié à votre application

Votre application peut être amenée à détecter si l’utilisateur s’est connecté via le flux d’utilisateur Mot de passe oublié. La revendication **isForgotPassword** contient une valeur booléenne qui indique qu’elle a été exécutée. La revendication peut être émise dans le jeton envoyé à votre demande. Si nécessaire, ajoutez **isForgotPassword** aux revendications de sortie dans la section **Partie de confiance** . Votre application peut vérifier la revendication **isForgotPassword** pour déterminer si l’utilisateur réinitialise son mot de passe.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Téléchargez la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Répertoires + Abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans le menu sous **Stratégies**, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**. Dans l’ordre suivant, téléchargez les deux fichiers de stratégie que vous avez modifiés :
   1. La stratégie d’extension, par exemple, *TrustFrameworkExtensions.xml*.
   1. La stratégie de la partie de confiance, par exemple *SignUpSignIn.xml*.

::: zone-end

### <a name="test-the-password-reset-flow"></a>Tester le flux de réinitialisation de mot de passe

1. Sélectionnez le flux d’utilisateur d’inscription ou de connexion (de type Recommandé) que vous souhaitez tester.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Pour **Application**, sélectionnez l’application Web nommée *application Web 1* que vous avez inscrite précédemment. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Mot de passe oublié ?** .
1. Vérifiez l’adresse e-mail du compte que vous avez créé précédemment, puis sélectionnez **Continuer**.
1. Dans la boîte de dialogue qui s’affiche, modifiez le mot de passe de l’utilisateur, puis sélectionnez **Continuer**. Le jeton est retourné à `https://jwt.ms` et le navigateur l’affiche.
1. Vérifiez la valeur de la revendication **isForgotPassword** du jeton de retour. Si elle existe et qu’elle a la valeur `true`, l’utilisateur a réinitialisé le mot de passe.

## <a name="password-reset-policy-legacy"></a>Stratégie de réinitialisation de mot de passe (hérité)

Si l’expérience de [réinitialisation du mot de passe libre-service](#self-service-password-reset-recommended) n’est pas activée, le fait de sélectionner ce lien ne déclenche pas automatiquement un flux de travail utilisateur de réinitialisation À la place, le code d’erreur `AADB2C90118` est retourné à votre application. Votre application doit gérer ce code d’erreur en réinitialisant la bibliothèque d’authentification pour authentifier un workflow d’utilisateur de réinitialisation de mot de passe Azure AD B2C.

Le diagramme suivant illustre le processus :

1. Dans l’application, l’utilisateur sélectionne **Se connecter**. L’application lance une demande d’autorisation et dirige l’utilisateur vers Azure AD B2C pour qu’il finaliser sa connexion. La demande d’autorisation spécifie un nom de stratégie d’inscription ou de connexion, tel que *B2C_1_signup_signin*.
1. L’utilisateur sélectionne le lien **Vous avez oublié votre mot de passe ?** . Azure AD B2C retourne le code d’erreur `AADB2C90118` à l’application.
1. L’application gère le code d’erreur et lance une nouvelle demande d’autorisation. La demande d’autorisation spécifie le nom de la stratégie de réinitialisation du mot de passe, par exemple *B2C_1_pwd_reset*.

    ![Diagramme illustrant le flux d’utilisateur de réinitialisation de mot de passe hérité.](./media/add-password-reset-policy/password-reset-flow-legacy.png)

vous pouvez voir un [exemple ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)de base, qui montre comment les flux d’utilisateurs sont liés.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Pour permettre aux utilisateurs de votre application de réinitialiser leur mot de passe, créez un flux d’utilisateur de réinitialisation du mot de passe.

1. Dans le portail Azure, accédez à la vue d’ensemble du locataire Azure AD B2C.
1. Dans le menu de gauche, sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans **Créer un flux d’utilisateur**, sélectionnez le flux d’utilisateur de **Réinitialisation de mot de passe** .
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Pour **nom**, entrez un nom pour le flux d’utilisateur. Par exemple, *passwordreset1*.
1. Sous **Fournisseurs d’identité**, activez **Réinitialiser le mot de passe à l’aide du nom d’utilisateur** ou **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.
1. Sous **Authentification multifacteur**, si vous voulez demander aux utilisateurs de vérifier leur identité avec une deuxième méthode d’authentification, choisissez le type de méthode et le moment auquel appliquer l’authentification multifacteur (MFA). [En savoir plus](multi-factor-authentication.md)
1. Sous **Accès conditionnel**, si vous avez configuré des stratégies d’accès conditionnel pour votre locataire Azure AD B2C et que vous souhaitez les utiliser dans ce workflow d’utilisateur, cochez la case **Appliquer les stratégies d’accès conditionnel**. Vous n’avez pas besoin de spécifier un nom de stratégie. [Plus d’informations](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Sous **Revendications d’application**, sélectionnez **Afficher plus**. Choisissez les revendications à renvoyer dans les jetons d’authentification qui sont redirigés vers votre application. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.
1. Sélectionnez **OK**.
1. Sélectionnez **Créer** pour ajouter le flux d’utilisateur. Un préfixe de *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

Pour tester le flux d’utilisateur :

1. Sélectionnez le flux d’utilisateur que vous avez créé. Dans la page de vue d’ensemble du flux utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application Web nommée *application Web 1* que vous avez inscrite précédemment. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux d’utilisateur**, vérifiez l’adresse e-mail du compte que vous avez créé précédemment, puis sélectionnez **Continuer**.
1. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est retourné à `https://jwt.ms` et le navigateur l’affiche.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et des stratégies de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Configurer une [réinitialisation de mot de passe forcée](force-password-reset.md)
