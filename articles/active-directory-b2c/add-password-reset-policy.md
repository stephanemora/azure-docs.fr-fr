---
title: Configurer un flux de réinitialisation de mot de passe dans Azure AD B2C
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux de réinitialisation de mot de passe dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2865138aeca012d30692e708a61e459492f03ccc
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109750696"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configuration d’un flux de réinitialisation de mot de passe dans Azure Active Directory B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Flux de réinitialisation de mot de passe

Le [parcours d’inscription et de connexion](add-sign-up-and-sign-in-policy.md) permet aux utilisateurs de réinitialiser leur mot de passe à l’aide du lien **Vous avez oublié votre mot de passe ?** . Le flux de réinitialisation du mot de passe est constitué des étapes suivantes :

1. Dans la page d’inscription et de connexion, l’utilisateur clique sur le lien **Vous avez oublié votre mot de passe ?** . Azure AD B2C lance le flux de réinitialisation du mot de passe. 
2. L’utilisateur fournit et vérifie son adresse e-mail avec un code unique horodaté.
3. L’utilisateur peut ensuite entrer un nouveau mot de passe.

![Flux de réinitialisation de mot de passe](./media/add-password-reset-policy/password-reset-flow.png)

Le flux de réinitialisation du mot de passe s’applique aux comptes locaux dans Azure AD B2C qui utilisent une [adresse e-mail](identity-provider-local.md#email-sign-in) ou un [nom d’utilisateur](identity-provider-local.md#username-sign-in) avec un mot de passe pour la connexion.

> [!TIP]
> Le flux de réinitialisation du mot de passe en libre-service permet aux utilisateurs de modifier leur mot de passe quand ils oublient leur mot de passe et souhaitent le réinitialiser. Envisagez de configurer un [flux de modification du mot de passe](add-password-change-policy.md) pour prendre en charge les cas où un utilisateur connaît son mot de passe et souhaite le modifier.

Une pratique courante après la migration des utilisateurs vers Azure AD B2C avec des mots de passe aléatoires consiste à demander aux utilisateurs de vérifier leurs adresses e-mail et de réinitialiser leur mot de passe lors de leur première connexion. Il est également courant de forcer l’utilisateur à réinitialiser son mot de passe après qu’un administrateur a modifié son mot de passe. Pour activer cette fonctionnalité, consultez [Forcer la réinitialisation du mot de passe](force-password-reset.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Réinitialisation du mot de passe en libre-service (recommandé)

La nouvelle expérience de réinitialisation de mot de passe fait maintenant partie de la stratégie d’inscription ou de connexion. Quand l’utilisateur sélectionne le lien **Vous avez oublié votre mot de passe ?** , il est immédiatement dirigé vers l’expérience Mot de passe oublié. Votre application n’a plus besoin de gérer le [code d’erreur AADB2C90118](#password-reset-policy-legacy) et vous n’avez pas besoin d’une stratégie distincte pour la réinitialisation du mot de passe.

::: zone pivot="b2c-user-flow"

L’expérience de réinitialisation du mot de passe en libre-service peut être configurée pour les flux d’utilisateur de **connexion (recommandé)** ou d’**inscription et de connexion (recommandé)** . Si vous n’avez pas d’utilisateur de ce type, créez un flux d’utilisateur [d’inscription et de connexion](add-sign-up-and-sign-in-policy.md). 

Pour activer la réinitialisation du mot de passe en libre-service pour le flux d’utilisateur d’inscription ou de connexion :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez un flux d’utilisateur d’inscription ou de connexion (de type **Recommandé**) que vous souhaitez personnaliser.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Configuration du mot de passe**, sélectionnez **Réinitialisation du mot de passe en libre-service**.
1. Sélectionnez **Enregistrer**.
1. Dans le menu de gauche, sous **Personnaliser**, sélectionnez **Mises en page**.
1. Dans la **version de mise en page**, choisissez **2.1.3** ou une version ultérieure.
1. Sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Les sections suivantes décrivent comment ajouter une expérience de mot de passe libre-service à une stratégie personnalisée. L’exemple est basé sur les fichiers de stratégie inclus dans le [pack de démarrage sur les stratégies personnalisées](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack). 

> [!TIP]
> Vous trouverez un exemple complet de la stratégie « inscription ou connexion avec réinitialisation de mot de passe » sur [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Indiquer qu’un utilisateur a sélectionné le lien Vous avez oublié votre mot de passe ?

Pour indiquer à la stratégie que l’utilisateur a sélectionné le lien **Vous avez oublié votre mot de passe ?** , définissez une revendication booléenne. Cette revendication sera utilisée pour diriger le parcours utilisateur vers le profil technique Mot de passe oublié. Cette revendication peut également être émise à destination du jeton pour que l’application soit informée que l’utilisateur s’est connecté via le flux Mot de passe oublié.

Vous déclarez vos revendications dans le [schéma de revendications](claimsschema.md). Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez la revendication suivante à l’élément **ClaimsSchema**. 

```XML
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

La [version de mise en page](contentdefinitions.md#migrating-to-page-layout) `2.1.2` est requise pour activer le flux de réinitialisation de mot de passe libre-service au cours du parcours d’inscription ou de connexion.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ContentDefinitions](contentdefinitions.md). Si l’élément n’existe pas, ajoutez-le.
1. Modifiez l’élément **DataURI** dans l’élément **ContentDefinition** avec l’ID **api.signuporsignin** comme indiqué ci-dessous.

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

Pour initier la revendication `isForgotPassword`, un profil technique de transformation des revendications est utilisé. Ce profil technique sera référencé ultérieurement. Lorsqu’il est appelé, il définit la valeur de la revendication `isForgotPassword` sur `true`. Recherchez l’élément `ClaimsProviders`. Si l’élément n’existe pas, ajoutez-le. Ajoutez ensuite le fournisseur de revendications suivant :  

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

Le profil technique `SelfAsserted-LocalAccountSignin-Email` `setting.forgotPasswordLinkOverride` définit l’exécution de l’échange de revendications de réinitialisation de mot de passe dans votre parcours utilisateur. 

### <a name="add-the-password-reset-sub-journey"></a>Ajouter le sous-parcours de réinitialisation du mot de passe

Votre parcours inclut à présent la possibilité pour l’utilisateur de se connecter, de s’inscrire et d’effectuer la réinitialisation du mot de passe. Pour mieux organiser le parcours utilisateur, vous pouvez utiliser un [sous-parcours](subjourneys.md) afin de gérer le flux de réinitialisation du mot de passe.

Le sous-parcours est appelé à partir du parcours utilisateur et exécute les étapes spécifiques pour fournir l’expérience de réinitialisation du mot de passe à l’utilisateur. Utilisez le sous-parcours de type `Call` de sorte que, une fois le sous-parcours terminé, le contrôle est renvoyé à l’étape d’orchestration qui a initié le sous-parcours.

Recherchez l’élément `SubJourneys`. Si l’élément n’existe pas, ajoutez-le après l’élément `User Journeys`. Ajoutez ensuite le sous-parcours suivant :

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

Vous devez connecter le lien **Vous avez oublié votre mot de passe ?** au sous-parcours Mot de passe oublié. Pour ce faire, référencez l’ID de sous-parcours Mot de passe oublié dans l’élément **ClaimsProviderSelection** de l’étape **CombinedSignInAndSignUp**.

Si vous n’avez pas votre propre parcours utilisateur personnalisé avec une étape **CombinedSignInAndSignUp**, utilisez la procédure suivante pour dupliquer un parcours utilisateur d’inscription ou de connexion existant. Sinon, passez à la section suivante.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Créez un élément enfant de l’élément **UserJourneys** en collant tout le contenu de l’élément **UserJourney** que vous avez copié à l’étape 2.
5. Renommez l’ID du parcours utilisateur. Par exemple : `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Connecter le lien Vous avez oublié votre mot de passe ? au sous-parcours Mot de passe oublié 

Dans votre parcours utilisateur, vous pouvez représenter le sous-parcours Mot de passe oublié en tant que **ClaimsProviderSelection**. L’ajout de cet élément permet de connecter le lien **Vous avez oublié votre mot de passe ?** au sous-parcours Mot de passe oublié.

1. Dans le parcours utilisateur, recherchez l’élément d’étape d’orchestration incluant `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"`. Il s’agit généralement de la première étape d’orchestration. L’élément **ClaimsProviderSelections** contient une liste de fournisseurs d’identité via lesquels un utilisateur peut se connecter. Ajoutez la ligne suivante :
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. À la prochaine étape d’orchestration, ajoutez un élément **ClaimsExchange**. Ajoutez la ligne suivante :

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Ajoutez l’étape d’orchestration suivante entre l’étape en cours et la suivante. La nouvelle étape d’orchestration vérifie si la revendication `isForgotPassword` existe. Si la revendication existe, elle appelle le [sous-parcours de réinitialisation de mot de passe](#add-the-password-reset-sub-journey). 

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

Maintenant que vous avez modifié ou créé un parcours utilisateur, dans la section **Partie de confiance**, spécifiez le parcours qu’Azure AD B2C exécutera pour cette stratégie personnalisée. Dans l’élément [RelyingParty](relyingparty.md), recherchez l’élément **DefaultUserJourney**. Mettez à jour l’élément **ReferenceId DefaultUserJourney** pour qu’il corresponde à l’ID du parcours utilisateur dans lequel vous avez ajouté **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Indiquer le flux Mot de passe oublié à votre application

Votre application peut être amenée à détecter si l’utilisateur s’est connecté via le flux d’utilisateur Mot de passe oublié. La revendication **isForgotPassword** contient une valeur booléenne qui indique cela, qui peut être émise dans le jeton envoyé à votre application. Si nécessaire, ajoutez `isForgotPassword` aux revendications de sortie dans la section **Partie de confiance**. Votre application peut vérifier la revendication `isForgotPassword` pour déterminer si l’utilisateur réinitialise son mot de passe.

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
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés dans l’ordre suivant :
   1. La stratégie d’extension, par exemple `TrustFrameworkExtensions.xml`.
   2. La stratégie de partie de confiance, par exemple `SignUpSignIn.xml`.

::: zone-end

### <a name="test-the-password-reset-flow"></a>Tester le flux de réinitialisation de mot de passe

1. Sélectionnez le flux d’utilisateur d’inscription ou de connexion (de type Recommandé) que vous souhaitez tester.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Vous avez oublié votre mot de passe ?** .
1. Vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
1. Vérifiez la valeur de revendication `isForgotPassword` du jeton de retour. Si elle existe et qu’elle a la valeur true, cela indique que l’utilisateur a réinitialisé le mot de passe.

## <a name="password-reset-policy-legacy"></a>Stratégie de réinitialisation de mot de passe (hérité)

Si l’expérience de [réinitialisation du mot de passe en libre-service](#self-service-password-reset-recommended) n’est pas activée, le fait de cliquer sur ce lien ne déclenche pas automatiquement un flux d’utilisateur de réinitialisation du mot de passe. À la place, le code d’erreur `AADB2C90118` est retourné à votre application. Votre application doit gérer ce code d’erreur en réinitialisant la bibliothèque d’authentification pour authentifier un flux d’utilisateur de réinitialisation de mot de passe Azure AD B2C.

Dans le schéma suivant :

1. À partir de l’application, l’utilisateur clique sur Se connecter. L’application lance une demande d’autorisation et dirige l’utilisateur vers Azure AD B2C pour qu’il finaliser sa connexion. La demande d’autorisation spécifie le nom de la stratégie d’inscription ou de connexion, par exemple **B2C_1_signup_signin**.
1. L’utilisateur sélectionne le lien **Vous avez oublié votre mot de passe ?** . Azure AD B2C retourne le code d’erreur AADB2C90118 à l’application.
1. L’application gère le code d’erreur et lance une nouvelle demande d’autorisation. La demande d’autorisation spécifie le nom de la stratégie de réinitialisation du mot de passe, par exemple **B2C_1_pwd_reset**.

![Flux d’utilisateur de réinitialisation de mot de passe hérité](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Pour voir un exemple, examinons un [simple exemple ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) qui illustre la liaison des flux d’utilisateur.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Pour permettre aux utilisateurs de votre application de réinitialiser leur mot de passe, utilisez un flux d’utilisateur de réinitialisation du mot de passe.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**. 
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *passwordreset1*.
1. Sous **Fournisseurs d’identité**, activez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.
1. Sous **Revendications d’application**, sélectionnez **Afficher plus** et choisissez les revendications à renvoyer à votre application dans les jetons d’autorisation. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.
1. Sélectionnez **OK**.
1. Sélectionnez **Créer** pour ajouter le flux d’utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux d’utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous pouvez maintenant modifier le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et la stratégie de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Configurer une [réinitialisation de mot de passe forcée](force-password-reset.md)
