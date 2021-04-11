---
title: Configurer les options du fournisseur de services SAML
title-suffix: Azure Active Directory B2C
description: Comment configurer les options du fournisseur de services SAML Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fea39388b6b4387dfc4fe95d1cdfb3e523a8089c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382434"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Options d’inscription d’une application SAML dans Azure AD B2C

Cet article décrit les options de configuration disponibles lors de la connexion Azure Active Directory (Azure AD B2C) à votre application SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="saml-response-signature"></a>Signature de réponse SAML

Vous pouvez spécifier un certificat à utiliser pour signer les messages SAML. Le message est l’élément `<samlp:Response>` dans la réponse SAML envoyée à l’application.

Si vous n’avez pas encore de clé de stratégie, [créez-en une](saml-service-provider.md#create-a-policy-key). Configurez ensuite l’élément de métadonnées `SamlMessageSigning` dans le profil technique d’émetteur de jeton SAML. Le `StorageReferenceId` doit référencer le nom de la clé de stratégie.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="saml-response-signature-algorithm"></a>Algorithme de signature de réponse SAML

Vous pouvez également configurer l’algorithme de signature utilisé pour chiffrer l’assertion SAML. Les valeurs possibles sont `Sha256`, `Sha384`, `Sha512` ou `Sha1`. Assurez-vous que le profil technique et l’application utilisent le même algorithme de signature. Utilisez uniquement l’algorithme pris en charge par votre certificat.

Configurez l’algorithme de signature à l’aide de la clé de métadonnées `XmlSignatureAlgorithm` dans l’élément de métadonnées de la partie de confiance.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-assertions-signature"></a>Signature des assertions SAML

Lorsque votre application s’attend à ce que la section d’assertion SAML soit signée, assurez-vous que le fournisseur de services SAML définit `WantAssertionsSigned` sur `true`. Si la valeur est `false` ou n’existe pas, la section d’assertion ne sera pas signée. L’exemple suivant montre les métadonnées d’un fournisseur de services SAML avec le paramètre `WantAssertionsSigned` défini sur `true`.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="saml-assertions-signature-certificate"></a>Certificat de signature des assertions SAML

Votre stratégie doit spécifier un certificat à utiliser pour signer la section d’assertions SAML de la réponse SAML. Si vous n’avez pas encore de clé de stratégie, [créez-en une](saml-service-provider.md#create-a-policy-key). Configurez ensuite l’élément de métadonnées `SamlAssertionSigning` dans le profil technique d’émetteur de jeton SAML. Le `StorageReferenceId` doit référencer le nom de la clé de stratégie.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="saml-assertions-encryption"></a>Chiffrement des assertions SAML

Lorsque votre application s’attend à ce que les assertions SAML soient dans un format chiffré, vous devez vérifier que le chiffrement est activé dans la stratégie de Azure AD B2C.

Azure AD B2C utilise le certificat de clé publique du fournisseur de services pour chiffrer l’assertion SAML. La clé publique doit exister dans le point de terminaison des métadonnées de l’application SAML avec le KeyDescriptor « use » défini sur « Encryption », comme illustré dans l’exemple suivant :

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Pour permettre à Azure AD B2C d’envoyer des assertions chiffrées, affectez la valeur true à l’élément de métadonnées **WantsEncryptedAssertion** `true`dans le [profil technique de la partie de confiance](relyingparty.md#technicalprofile). Vous pouvez également configurer l’algorithme utilisé pour chiffrer l’assertion SAML.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Méthode de chiffrement

Pour configurer la méthode de chiffrement utilisée pour chiffrer les données d’assertion SAML, définissez la clé de métadonnées `DataEncryptionMethod` dans la partie de confiance. Les valeurs possibles sont `Aes256` (par défaut), `Aes192`, `Sha512` ou `Aes128`. Les métadonnées contrôlent la valeur de l’élément `<EncryptedData>` dans la réponse SAML.

Pour configurer la méthode de chiffrement utilisée pour chiffrer la copie de la clé, qui a été utilisée pour les données d’assertion SAML, définissez la clé de métadonnées `KeyEncryptionMethod` dans la partie de confiance. Les valeurs possibles sont `Rsa15` (par défaut) - Algorithme version 1.5 PKCS (Public Key Cryptography Standard) RSA et `RsaOaep` - Algorithme de chiffrement OAEP (Optimal Asymmetric Encryption Padding) RSA.  Les métadonnées contrôlent la valeur de l’élément `<EncryptedKey>` dans la réponse SAML.

L’exemple suivant illustre la section `EncryptedAssertion` d’une assertion SAML. La méthode de données chiffrées est `Aes128` et la méthode de clé chiffrée est `Rsa15`.

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Vous pouvez modifier le format des assertions chiffrées. Pour configurer le format de chiffrement, définissez la clé de métadonnées `UseDetachedKeys` dans la partie de confiance. Valeurs possibles : `true` ou `false` (par défaut). Lorsque la valeur est définie sur `true`, les clés détachées ajoutent l’assertion chiffrée en tant qu’enfant de `EncrytedAssertion` lieu de `EncryptedData`.

Configurez la méthode et le format de chiffrement, utilisez les clés de métadonnées dans le [profil technique de la partie de confiance](relyingparty.md#technicalprofile) :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Flux lancé par le fournisseur d’identité

Lorsque votre application s’attend à recevoir une assertion SAML sans envoyer d’abord une demande d’authentification SAML au fournisseur d’identité, vous devez configurer Azure AD B2C pour le flow initié par le fournisseur d’identité.

Dans le flux lancé par le fournisseur d’identité, le processus de connexion est lancé par le fournisseur d’identité (Azure AD B2C), qui envoie une réponse SAML non sollicitée au fournisseur de services (votre application par partie de confiance).

Actuellement, nous ne prenons pas en charge les scénarios où le fournisseur d’identité initiateur est un fournisseur d’identité externe fédéré par Azure AD B2C, par exemple [AD-FS](identity-provider-adfs.md) ou [Salesforce](identity-provider-salesforce-saml.md). Il est uniquement pris en charge pour l’authentification de compte local Azure AD B2C.

Pour activer le flux lancé par le fournisseur d’identité, définissez l’élément de métadonnées **IdpInitiatedProfileEnabled** sur `true` dans le [profil technique de la partie de confiance](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Pour vous connecter ou inscrire un utilisateur par le biais d’un flux lancé par le fournisseur d’identité, utilisez l’URL suivante :

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Remplacez les valeurs suivantes :

* **tenant-name** par le nom de votre locataire
* **policy-name** par le nom de votre stratégie de partie de confiance SAML.
* **app-identifier-uri** avec `identifierUris` dans le fichier de métadonnées, par exemple `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Exemple de stratégie

Nous fournissons un exemple complet de stratégie que vous pouvez utiliser à des fins de test avec l’application de test SAML.

1. Téléchargez l’[exemple de stratégie de connexion SAML-SP-initiated](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Mettez à jour `TenantId` pour qu’il corresponde à votre nom de locataire, par exemple *contoso.b2clogin.com*.
1. Conservez le nom de la stratégie *B2C_1A_signup_signin_saml*.

## <a name="saml-response-lifetime"></a>Durée de vie de la réponse SAML

Vous pouvez configurer la durée de validité de la réponse SAML. Définissez la durée de vie à l’aide de l’élément de métadonnées `TokenLifeTimeInSeconds` dans le profil technique émetteur de jeton SAML. Cette valeur est le nombre de secondes qui peuvent s’écouler à partir du timestamp `NotBefore` calculé au moment de l’émission du jeton. La durée de vie par défaut est de 300 secondes (5 minutes).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>La réponse SAML est valide à partir du décalgae

Vous pouvez configurer le décalage horaire appliqué au timestamp `NotBefore` de la réponse SAML. Cette configuration garantit que si les heures entre deux plateformes ne sont pas synchronisées, l’assertion SAML est toujours considérée comme valide quand elle est dans ce temps.

Définissez le décalage horaire à l’aide de l’élément de métadonnées `TokenNotBeforeSkewInSeconds` dans le profil technique émetteur de jeton SAML. La valeur de décalage est donnée en secondes, avec 0 comme valeur par défaut. La valeur maximale est 3600 (une heure).

Par exemple, lorsque `TokenNotBeforeSkewInSeconds` est défini sur `120` secondes :

- Le jeton est émis à 13:05:10 UTC
- Le jeton est valide à partir de 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Supprimer les millisecondes de la date et de l’heure

Vous pouvez indiquer si les millisecondes seront supprimées des valeurs de DateHeure dans la réponse SAML (notamment IssueInstant, NotBefore, NotOnOrAfter et AuthnInstant). Pour supprimer les millisecondes, définissez la clé de métadonnées `RemoveMillisecondsFromDateTime
` dans la partie de confiance. Valeurs possibles : `false` (par défaut) ou `true`.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>ID émetteur Azure AD B2C

Si vous avez plusieurs applications SAML qui dépendent de différentes valeurs `entityID`, vous pouvez remplacer la valeur `issueruri` dans votre fichier de partie de confiance. Pour remplacer l’URI émetteur, copiez le profil technique avec l’ID « Saml2AssertionIssuer » à partir du fichier de base et remplacez la valeur `issueruri`.

> [!TIP]
> Copiez la section `<ClaimsProviders>` à partir de la base et conservez ces éléments dans le fournisseur de revendications : `<DisplayName>Token Issuer</DisplayName>`, `<TechnicalProfile Id="Saml2AssertionIssuer">` et `<DisplayName>Token Issuer</DisplayName>`.
 
Exemple :

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Gestion des sessions

Vous pouvez gérer la session entre Azure AD B2C et l’application par partie de confiance SAML à l’aide de l’élément `UseTechnicalProfileForSessionManagement` et de [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="force-users-to-reauthenticate"></a>Contraindre les utilisateurs à s’authentifier à nouveau 

Pour contraindre les utilisateurs à s’authentifier à nouveau, l’application peut inclure l’attribut `ForceAuthn` dans la demande d’authentification SAML. L’attribut `ForceAuthn` est une valeur booléenne. Quand la valeur est définie sur true, la session utilisateurs est invalidée sur Azure AD B2C, et l’utilisateur est contraint de s’authentifier de nouveau. La requête d’authentification SAML suivante montre comment affecter la valeur d’attribut `ForceAuthn` sur true. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>Signer les métadonnées SAML d’IdP Azure AD B2C

Vous pouvez demander à Azure AD B2C de signer son document de métadonnées d’IdP SAML si l’application le requiert. Si vous n’avez pas encore de clé de stratégie, [créez-en une](saml-service-provider.md#create-a-policy-key). Configurez ensuite l’élément de métadonnées `MetadataSigning` dans le profil technique d’émetteur de jeton SAML. Le `StorageReferenceId` doit référencer le nom de la clé de stratégie.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>Déboguer le protocole SAML

Pour faciliter la configuration et le débogage de l’intégration avec un fournisseur de services, vous pouvez utiliser une extension de navigateur pour le protocole SAML, par exemple l’[Extension SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pour Chrome, le [Traceur SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pour Firefox ou les [Outils de développement Edge ou IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

À l’aide de ces outils, vous pouvez vérifier l’intégration entre votre application et Azure AD B2C. Par exemple :

* Vérifiez si la requête SAML contient une signature, et déterminez l’algorithme utilisé pour la connexion à la demande d’autorisation.
* Vérifiez si Azure AD B2C renvoie un message d’erreur.
* Vérifiez si la section d’assertion est chiffrée.

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez plus d’informations relatives au [protocole SAML sur le site web OASIS](https://www.oasis-open.org/).
- Récupérez l’application web SAML test à partir du [référentiel Azure AD B2C de la communauté GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
