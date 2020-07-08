---
title: Configurer Azure AD B2C en tant qu’IdP SAML pour vos applications
title-suffix: Azure AD B2C
description: Comment configurer Azure AD B2C pour fournir des assertions de protocole SAML à vos applications (fournisseurs de services). Azure AD B2C agira en tant que fournisseur d’identité unique (IdP) à votre application SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b9ea9e756587af124ca94518d9f15271310ddee3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389376"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Inscrire une application SAML dans Azure AD B2C

Dans cet article, vous allez apprendre à configurer Azure Active Directory B2C (Azure AD B2C) pour agir en tant que fournisseur d’identité (IdP) Security Assertion Markup Language (SAML) pour vos applications.

## <a name="scenario-overview"></a>Présentation du scénario

Les organisations qui utilisent Azure AD B2C comme solution de gestion des identités et des accès des clients peuvent nécessiter une interaction avec les fournisseurs d’identité ou les applications qui sont configurés pour s’authentifier à l’aide du protocole SAML.

Azure AD B2C atteint l’interopérabilité SAML de l’une des deux manières suivantes :

* En agissant en tant que *fournisseur d’identité* (IdP) et en obtenant une authentification unique (SSO) avec des fournisseurs de services basés sur SAML (vos applications)
* En agissant en tant que *fournisseur de services* (SP) et en interagissant avec des fournisseurs d’identité SAML tels que Salesforce et ADFS

![Diagramme avec B2C comme fournisseur d’identité sur la gauche et B2C en tant que fournisseur de services à droite](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Résumé des deux scénarios de base non exclusifs avec SAML :

| Scénario | Rôle Azure AD B2C | Procédures |
| -------- | ----------------- | ------- |
| Mon application attend une assertion SAML pour effectuer une authentification. | **Azure AD B2C agit en tant que fournisseur d’identité (IdP)**<br />Azure AD B2C agit comme un IdP SAML pour les applications. | Cet article. |
| Mes utilisateurs ont besoin d’une authentification unique avec un fournisseur d’identité conforme à SAML comme ADFS, Salesforce ou Shibboleth.  | **Azure AD B2C agit en tant que fournisseur de services (SP)**<br />Azure AD B2C agit en tant que fournisseur de services lors de la connexion au fournisseur d’identité SAML. Il s’agit d’un proxy de fédération entre votre application et le fournisseur d’identité SAML.  | <ul><li>[Configurer la connexion avec ADFS comme fournisseur d’identité SAML à l’aide de stratégies personnalisées](identity-provider-adfs2016-custom.md)</li><li>[Configurer la connexion avec un fournisseur SAML Salesforce en utilisant des stratégies personnalisées](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prérequis

* Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](custom-policy-get-started.md). Vous avez besoin de la stratégie personnalisée *SocialAndLocalAccounts* à partir du Pack de démarrage de stratégie personnalisée abordé dans l’article.
* Notions de base sur le protocole SAML (Security Assertion Markup Language).
* Une application web configurée en tant que fournisseur de services SAML (SP). Pour ce didacticiel, vous pouvez utiliser une [application de test SAML][samltest] que nous fournissons.

## <a name="components-of-the-solution"></a>Composants de la solution

Trois composants principaux sont requis pour ce scénario :

* Le **fournisseur de services** SAML avec la possibilité d’envoyer des demandes SAML, et de recevoir et de décoder des assertions SAML, et d’y répondre, à partir d’Azure AD B2C. On parle également de la partie de confiance.
* Le **point de terminaison de métadonnées** SAML disponible publiquement pour votre fournisseur de services.
* [Locataire Azure AD B2C](tutorial-create-tenant.md)

Si vous ne disposez pas encore d’un fournisseur de services SAML et d’un point de terminaison de métadonnées associé, vous pouvez utiliser cet exemple d’application SAML que nous avons mis à disposition à des fins de test :

[Application de test SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurer des certificats

Pour établir une relation d'approbation entre votre fournisseur de services et Azure AD B2C, vous devez fournir les certificats X509 de l'application web.

* **Certificats du fournisseur de services**
  * Certificat avec une clé privée stockée dans votre application web. Ce certificat est utilisé par votre fournisseur de services pour signer la demande SAML envoyée à Azure AD B2C. Azure AD B2C lit la clé publique à partir des métadonnées du fournisseur de services pour valider la signature.
  * (Facultatif) Certificat avec une clé privée stockée dans votre application web. Azure AD B2C lit la clé publique à partir des métadonnées du fournisseur de services pour chiffrer l’assertion SAML. Le fournisseur de services utilise ensuite la clé privée pour déchiffrer l’assertion.
* **Certificats Azure AD B2C**
  * Certificat avec une clé privée dans Azure AD B2C. Ce certificat est utilisé par Azure AD B2C pour signer la réponse SAML envoyée à votre fournisseur de services. Votre fournisseur de services lit la clé publique des métadonnées Azure AD B2C pour valider la signature de la réponse SAML.

Vous pouvez utiliser un certificat émis par une autorité de certification publique ou, dans le cadre de ce didacticiel, un certificat auto-signé.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Préparer un certificat auto-signé

Si vous n’avez pas encore de certificat, vous pouvez utiliser un certificat auto-signé pour ce didacticiel. Sur Windows, vous pouvez utiliser l’applet de commande [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) de PowerShell pour générer un certificat.

1. Exécutez cette commande PowerShell pour générer un certificat auto-signé. Modifiez l’argument `-Subject` comme il convient pour votre application et le nom de locataire Azure AD B2C. Vous pouvez également ajuster la date de `-NotAfter` pour spécifier un délai d’expiration différent pour le certificat.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Open **Gérer les certificats utilisateur** > **Utilisateur actuel** > **Personnel** > **Certificats** > *yourappname.yourtenant.onmicrosoft.com*
1. Sélectionnez le certificat > **Action** > **Toutes les tâches** > **Exporter**
1. Sélectionnez **Oui** > **Suivant** > **Oui, exporter la clé privée** > **Suivant**
1. Acceptez les valeurs par défaut pour **Format de fichier d’exportation**
1. Fournissez un mot de passe pour le certificat

### <a name="12-upload-the-certificate"></a>1.2 Charger le certificat

Ensuite, chargez le certificat de signature d’assertion et de réponse SAML sur Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre locataire Azure AD B2C.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**, puis **Clés de stratégie**.
1. Sélectionnez **Ajouter**, puis sélectionnez **Options**  > **Télécharger**.
1. Entrez un **Nom**, par exemple *SamlIdpCert*. Le préfixe *B2C_1A_* est ajouté automatiquement au nom de votre clé.
1. Téléchargez votre certificat à l’aide du contrôle de chargement de fichier.
1. Entrez le mot de passe du certificat.
1. Sélectionnez **Create** (Créer).
1. Vérifiez que la clé s’affiche comme prévu. Par exemple, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Préparer votre stratégie

### <a name="21-create-the-saml-token-issuer"></a>2.1 Créer l’émetteur de jeton SAML

À présent, ajoutez la capacité de votre locataire à émettre des jetons SAML à l’aide des profils techniques [émetteur de jeton SAML](saml-issuer-technical-profile.md) et [fournisseur de session SAML](custom-policy-reference-sso.md#samlssosessionprovider).

Ouvrez `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** dans le pack de démarrage de stratégie personnalisée.

Recherchez la section `<ClaimsProviders>` et ajoutez l’extrait de code XML suivant.

Vous pouvez modifier la valeur des métadonnées de `IssuerUri`. Il s’agit de l’URI de l’émetteur qui est retourné dans la réponse SAML d’Azure AD B2C. Votre application par partie de confiance doit être configurée pour accepter un URI d’émetteur lors de la validation de l’assertion SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Ajouter la stratégie de partie de confiance SAML

Maintenant que votre locataire peut émettre des assertions SAML, vous devez créer la stratégie de partie de confiance SAML et modifier le parcours utilisateur afin qu’il émette une assertion SAML au lieu d’un JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Créer une stratégie d’inscription ou de connexion

1. Créez une copie du fichier *SignUpOrSignin.xml* dans le répertoire de travail de votre pack de démarrage, puis enregistrez-le sous un nouveau nom. Par exemple, *SignUpOrSigninSAML.xml*. Il s’agit de votre fichier de stratégie de partie de confiance.

1. Ouvrez le fichier *SignUpOrSigninSAML.xml* dans votre éditeur préféré.

1. Modifiez les `PolicyId` et `PublicPolicyUri` de la stratégie pour _B2C_1A_signup_signin_saml_ et `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` comme indiqué ci-dessous.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Ajoutez l’extrait de code XML suivant juste avant l’élément `<RelyingParty>`. Ce code XML remplace l’étape d’orchestration numéro 7 du parcours utilisateur _SignUpOrSignIn_. Si vous avez démarré à partir d’un autre dossier du pack de démarrage, ou si vous avez personnalisé votre parcours utilisateur en ajoutant ou en supprimant des étapes d’orchestration, vérifiez que le nombre (dans l’élément `order`) est aligné avec celui spécifié dans le parcours utilisateur pour l’étape émetteur du jeton (par exemple, dans les autres dossiers du pack de démarrage, il s’agit du numéro d’étape 4 pour `LocalAccounts`, 6 pour `SocialAccounts` et 9 pour `SocialAndLocalAccountsWithMfa`).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Remplacez la totalité de l’élément `<TechnicalProfile>` dans l’élément `<RelyingParty>` par le fichier XML de profil technique suivant.

    ```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Mettez à jour `tenant-name` avec le nom de votre locataire Azure AD B2C.

Votre fichier de stratégie de partie de confiance final doit ressembler à ce qui suit :

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Charger et tester vos métadonnées de stratégie

Enregistrez vos modifications et chargez le nouveau fichier de stratégie. Une fois que vous avez chargé les deux stratégies (l’extension et les fichiers de la partie de confiance), ouvrez un navigateur Web et accédez aux métadonnées de la stratégie.

Les métadonnées IDP de la stratégie Azure AD B2C sont des informations utilisées dans le protocole SAML pour exposer la configuration d’un fournisseur d’identité SAML. Elles définissent l’emplacement des services, tels que la connexion et la déconnexion, les certificats, la méthode de connexion, et bien plus encore. Les métadonnées de la stratégie Azure AD B2C sont disponibles à l’adresse URL suivante. Remplacez `tenant-name` par le nom de votre locataire Azure AD B2C et `policy-name` par le nom (ID) de la stratégie :

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Votre stratégie personnalisée et votre locataire Azure AD B2C sont maintenant prêts. Ensuite, créez une inscription d’application dans Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Installer l’application dans le répertoire Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 Inscrire votre application dans Azure AD B2C

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *SAMLApp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire d’organisation uniquement**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://localhost`. Vous modifiez cette valeur plus tard dans le manifeste de l’inscription de l’application.
1. Sélectionnez **Inscription**.

### <a name="42-update-the-app-manifest"></a>4.2 Mettre à jour le manifeste d’application

Pour les applications SAML, vous devez configurer plusieurs propriétés dans le manifeste de l’inscription de l’application.

1. Dans le [Portail Azure](https://portal.azure.com), accédez à l’inscription de l’application que vous avez créée dans la section précédente.
1. Sous **Gérer**, sélectionnez **Manifeste** pour ouvrir l’éditeur de manifeste. Vous pouvez modifier plusieurs propriétés dans les sections suivantes.

#### <a name="identifieruris"></a>identifierUris

Le `identifierUris` est une collection de chaînes contenant un ou plusieurs URI définis par l’utilisateur qui identifient de façon unique une application web au sein de son locataire Azure AD B2C. Votre fournisseur de services doit définir cette valeur dans l’élément `Issuer` d’une demande SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Cette propriété représente l’URL des métadonnées disponibles publiquement du fournisseur de services. L’URL de métadonnées peut pointer vers un fichier de métadonnées chargé sur n’importe quel point de terminaison accessible de manière anonyme, par exemple le stockage d’objets BLOB.

Les métadonnées sont des informations utilisées dans le protocole SAML pour exposer la configuration d’un tiers SAML, tel qu’un fournisseur de services. Elles définissent l’emplacement des services tels que la connexion et la déconnexion, les certificats, la méthode de connexion et bien plus encore. Azure AD B2C lit les métadonnées du fournisseur de services et agit en conséquence. Les métadonnées ne sont pas requises. Vous pouvez également spécifier certains attributs, tels que l’URI de réponse et l’URI de déconnexion, directement dans le manifeste de l’application.

Si des propriétés sont spécifiées *à la fois* dans l’URL des métadonnées SAML et dans le manifeste de l’inscription de l’application, elles sont **fusionnées**. Les propriétés spécifiées dans l’URL des métadonnées sont traitées en premier et sont prioritaires.

Pour ce didacticiel qui utilise l’application de test SAML, utilisez la valeur suivante pour `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (facultatif)

Si vous ne fournissez pas d’URI de métadonnées, vous pouvez spécifier explicitement l’URL de réponse. Cette propriété facultative représente le `AssertionConsumerServiceUrl` (URL`SingleSignOnService` dans les métadonnées du fournisseur de services) et le `BindingType` est supposé être `HTTP POST`.

Si vous choisissez de configurer l’URL de réponse et l’URL de déconnexion dans le manifeste de l’application sans utiliser les métadonnées du fournisseur de services, Azure AD B2C ne valide pas la signature de la demande SAML et ne chiffre pas la réponse SAML.

Pour ce didacticiel, dans lequel vous utilisez l’application SAML test, définissez la propriété `url` de `replyUrlsWithType` sur la valeur indiquée dans l’extrait de code JSON suivant.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (facultatif)

Cette propriété facultative représente l’URL `Logout` (URL`SingleLogoutService` dans les métadonnées de la partie de confiance), et le `BindingType` correspondant est supposé être `Http-Redirect`.

Pour ce didacticiel qui utilise l’application de test SAML, laissez `logoutUrl` défini sur `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Mettre à jour votre code d’application

La dernière étape consiste à activer Azure AD B2C en tant qu’IdP SAML dans votre application par partie de confiance SAML. Chaque application est différente et les étapes à suivre varient. Pour plus d’informations, consultez la documentation de votre application.

Quelques-uns ou l’ensemble des éléments suivants sont généralement requis :

* **Métadonnées** : `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Émetteur** :   Utilisez entityID dans le fichier de métadonnées
* **URL de connexion/point de terminaison SAML/URL SAML** : Vérifiez la valeur dans le fichier de métadonnées
* **Certificat** : C’est *B2C_1A_SamlIdpCert*, mais sans la clé privée. Pour obtenir la clé publique du certificat :

    1. Accédez à l’URL des métadonnées spécifiée ci-dessus.
    1. Copiez la valeur dans l’élément `<X509Certificate>`.
    1. Collez-la dans un fichier texte.
    1. Enregistrez le fichier texte sous la forme d’un fichier *.cer*.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Tester avec l’application de test SAML (facultatif)

Pour suivre ce didacticiel à l’aide de notre [application SAML test][samltest] :

* Mettez à jour le nom du locataire
* Mettez à jour le nom de la stratégie, par exemple *B2C_1A_signup_signin_saml*
* Spécifiez cet URI d’émetteur : `https://contoso.onmicrosoft.com/app-name`

Sélectionnez **Connexion**. Un écran de connexion utilisateur doit s’afficher. Lors de la connexion, une assertion SAML est renvoyée à l’exemple d’application.

## <a name="sample-policy"></a>Exemple de stratégie

Nous fournissons un exemple complet de stratégie que vous pouvez utiliser à des fins de test avec l’application de test SAML.

1. Téléchargez l’[exemple de stratégie de connexion SAML-SP-initiated](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Mettez à jour `TenantId` pour qu’il corresponde à votre nom de locataire, par exemple *contoso.b2clogin.com*
1. Conservez le nom de la stratégie *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalités SAML prises en charge et non prises en charge

Les scénarios de la partie de confiance SAML suivants sont pris en charge via votre propre point de terminaison de métadonnées :

* Plusieurs URL de déconnexion ou une liaison POST pour l’URL de déconnexion dans l’objet principal du service ou de l’application.
* Spécifiez la clé de signature pour vérifier les demandes de partie de confiance dans l’objet principal du service ou de l’application.
* Spécifiez la clé de chiffrement de jeton dans l’objet principal du service ou de l’application.

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez plus d’informations relatives au [protocole SAML sur le site web OASIS](https://www.oasis-open.org/).
- Récupérez l’application web SAML test à partir du [dépôt Azure AD B2C de la communauté GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
