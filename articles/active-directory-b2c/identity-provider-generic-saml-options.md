---
title: Définir une connexion avec des options de fournisseur d’identité SAML
titleSuffix: Azure Active Directory B2C
description: Configurez les options de fournisseur d’identité (IdP) SAML de connexion dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488871"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Configurer les options de fournisseur d’identité SAML avec Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) prend en charge la fédération avec des fournisseurs d’identité SAML 2.0. Cet article décrit les options de configuration disponibles lors de l’activation d’une connexion avec un fournisseur d’identité SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mappage des revendications

L’élément **OutputClaims** contient une liste de revendications que le fournisseur d’identité SAML retourne. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d’identité. Vérifiez votre fournisseur d’identité pour obtenir la liste des revendications (assertions). Vous pouvez également vérifier le contenu de la réponse SAML que votre fournisseur d’identité retourne. Pour plus d’informations, consultez [Déboguer des messages SAML](#debug-saml-protocol). Pour ajouter une revendication, commencez par [définir une revendication](claimsschema.md), puis ajoutez celle-ci à la collection de revendications de sortie.

Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut `DefaultValue`. La valeur par défaut peut être statique ou dynamique, utilisant des [revendications de contexte](#enable-use-of-context-claims).

L’élément de revendication de sortie contient les attributs suivants :

- **ClaimTypeReferenceId** est la référence à un type de revendication. 
- **PartnerClaimType** : nom de la propriété qui apparaît dans l’assertion SAML. 
- **DefaultValue** est une valeur par défaut prédéfinie. Si la revendication est vide, la valeur par défaut est utilisée. Vous pouvez également vous servir d’un [outil de résolution des revendications](claim-resolver-overview.md) avec une valeur contextuelle, telle que l’ID d’application ou l’adresse IP de l’utilisateur.

### <a name="subject-name"></a>Nom d'objet

Pour lire l’assertion SAML **NamedId** dans l’**Objet** comme une revendication normalisée, définissez la revendication **PartnerClaimType** sur la valeur de l’attribut `SPNameQualifier`. Si l’attribut `SPNameQualifier` n’est pas présenté, définissez la revendication **PartnerClaimType** sur la valeur de l’attribut `NameQualifier`.

Assertion SAML :

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Revendication de sortie :

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Si les attributs `SPNameQualifier` ou `NameQualifier` ne sont pas présentés dans l’assertion SAML, définissez la revendication **PartnerClaimType** sur `assertionSubjectName`. Assurez-vous que **NameId** est la première valeur dans l’assertion XML. Lorsque vous définissez plusieurs assertion, Azure AD B2C sélectionne la valeur d’objet de la dernière assertion.


## <a name="configure-saml-protocol-bindings"></a>Configurer des liaisons de protocole SAML

Les demandes SAML sont envoyées au fournisseur d’identité comme spécifié dans l’élément `SingleSignOnService` des métadonnées du fournisseur d’identité. La plupart des demandes d’autorisation des fournisseurs d’identité sont transportées directement dans la chaîne de requête d’URL d’une requête HTTP GET (car les messages sont relativement courts). Pour savoir comment configurer les liaisons pour les deux demandes SAML, consultez la documentation de votre fournisseur d’identité.

Voici un exemple de service d’authentification unique de métadonnées Azure AD avec deux liaisons. La requête `HTTP-Redirect` prend le pas sur la requête `HTTP-POST`, car elle apparaît en premier dans les métadonnées du fournisseur d’identité SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

Les réponses SAML sont transmises à Azure AD B2C via une liaison HTTP POST. Les métadonnées de stratégie Azure AD B2C définissent la liaison `AssertionConsumerService` sur `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST`.

Voici un exemple d’assertion consumer service de métadonnées de stratégie Azure AD B2C.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Configurer la signature de demande SAML

Azure AD B2C signe toutes les demandes d’authentification sortantes à l’aide de la clé de chiffrement **SamlMessageSigning**. Pour désactiver la signature de demande SAML, définissez la valeur de **WantsSignedRequests** sur `false` . Si la métadonnée est définie sur `false`, les paramètres **SigAlg** et **Signature** (chaîne de requête ou paramètre de publication) sont omis de la demande.

Cette métadonnée contrôle également l’attribut **AuthnRequestsSigned** inclus avec la métadonnée du profil technique Azure AD B2C partagé avec le fournisseur d’identité. Azure AD B2C ne signe pas la demande si la valeur de **WantsSignedRequests** dans les métadonnées du profil technique est définie sur `false` et si les métadonnées du fournisseur identité **WantAuthnRequestsSigned** sont définies sur `false` ou ne sont pas spécifiées.

L’exemple suivant supprime la signature du certificat SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algorithme de signature

Azure AD B2C utilise `Sha1` pour signer la demande SAML. Utilisez les métadonnées **XmlSignatureAlgorithm** pour configurer l’algorithme à utiliser. Les valeurs possibles sont `Sha256`, `Sha384`, `Sha512` ou `Sha1` (par défaut). Ces métadonnées contrôlent la valeur du paramètre **SigAlg** (chaîne de requête ou paramètre d’envoi) dans la demande SAML. Veillez à configurer l’algorithme de signature des deux côtés avec la même valeur. Utilisez uniquement l’algorithme pris en charge par votre certificat.

### <a name="include-key-info"></a>Inclure les informations sur la clé

Lorsque le fournisseur d’identité indique que la liaison Azure AD B2C a la valeur `HTTP-POST`, Azure ad B2C inclut la signature et l’algorithme dans le corps de la demande SAML. Vous pouvez également configurer Azure AD pour inclure la clé publique du certificat lorsque la liaison a la valeur `HTTP-POST`. Utilisez la métadonnée **IncludeKeyInfo** pour définir sur `true` ou `false`. Dans l’exemple suivant, Azure AD n’inclut pas la clé publique du certificat.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Configurer l’ID de nom de demande SAML

L’élément `<NameID>` de demande d’autorisation SAML indique le format d’identificateur de nom SAML. Cette section décrit la configuration par défaut et explique comment personnaliser l’élément ID de nom.

## <a name="preferred-username"></a>Nom d’utilisateur par défaut

Pendant le parcours utilisateur pour la connexion, une application par partie de confiance peut cibler un utilisateur spécifique. Azure AD B2C vous permet d’envoyer un nom d’utilisateur de prédilection au fournisseur d’identité SAML. L’élément **InputClaims** est utilisé pour envoyer un **NameId** dans l’**Objet** de la demande d’autorisation SAML.

Pour inclure l’ID de nom de l’objet dans la demande d’autorisation, ajoutez l’élément `<InputClaims>` suivant immédiatement après les `<CryptographicKeys>`. La valeur de **PartnerClaimType** doit être `subject`.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

Dans cet exemple, Azure AD B2C envoie la valeur de la revendication **signInName** avec **NameId** dans l’**Objet** de la demande d’autorisation SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Vous pouvez utiliser des [revendications de contexte](claim-resolver-overview.md) telles que `{OIDC:LoginHint}` pour remplir la valeur de la revendication.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Format de stratégie d’ID de nom

Par défaut, la demande d’autorisation SAML spécifie la stratégie `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. Cela indique que tout type d’identificateur pris en charge par le fournisseur d’identité pour l’objet demandé peut être utilisé.

Pour modifier ce comportement, consultez la documentation de votre fournisseur d’identité qui contient des indications concernant les stratégies d’ID de nom prises en charge. Ajoutez ensuite la métadonnée `NameIdPolicyFormat` dans le format de stratégie correspondant. Par exemple :

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

La demande d’autorisation SAML suivante contient la stratégie d’ID de nom.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Autoriser la création de comptes

Si vous spécifiez le [format de stratégie d’ID de nom](#name-id-policy-format), vous pouvez également spécifier la propriété `AllowCreate` de **NameIDPolicy** pour indiquer si le fournisseur d’identité est autorisé à créer un compte au cours du processus de connexion. Pour obtenir des indications, consultez la documentation de votre fournisseur d’identité.

Azure AD B2C omet la propriété `AllowCreate` par défaut. Vous pouvez changer ce comportement à l’aide la métadonnée `NameIdPolicyAllowCreate`. La valeur de cette métadonnée est `true` ou `false`.

L’exemple suivant montre comment définir la propriété `AllowCreate` de l’élément `NameIDPolicy` sur `true`.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


L’exemple suivant illustre une demande d’autorisation avec la propriété **AllowCreate** de l’élément **NameIDPolicy** dans la demande d’autorisation.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Inclure les références de classe de contexte d’authentification

Une demande d’autorisation SAML peut contenir un élément **AuthnContext** qui spécifie le contexte d’une demande d’autorisation. L’élément peut contenir une référence de classe de contexte d’authentification, indiquant au fournisseur d’identité SAML le mécanisme d’authentification à présenter à l’utilisateur.

Pour configurer les références de classe de contexte d’authentification, ajoutez la métadonnée **IncludeAuthnContextClassReferences**. Dans la valeur, spécifiez une ou plusieurs références d’URI identifiant des classes de contexte d’authentification. Spécifiez plusieurs URI sous la forme d’une liste délimitée par des virgules. Pour obtenir des conseils concernant les URI **AuthnContextClassRef** pris en charge, consultez la documentation de votre fournisseur d’identité.

L’exemple suivant permet aux utilisateurs de se connecter avec un nom d’utilisateur et un mot de passe, ainsi que de se connecter avec un nom d’utilisateur et un mot de passe via une session protégée (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

La demande d’autorisation SAML suivante contient les références de classe de contexte d’authentification.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Inclure des données personnalisées dans la demande d’autorisation

Vous pouvez éventuellement inclure des éléments d’extension de message de protocole qui sont approuvés tant par Azure AD BC que votre fournisseur d’identité. L’extension est présentée au format XML. Vous incluez des éléments d’extension en ajoutant des données XML à l’intérieur de l’élément CDATA `<![CDATA[Your IDP metadata]]>`. Pour voir si l’élément d’extensions est pris en charge, consultez la documentation de votre fournisseur d’identité.

L’exemple suivant illustre l’utilisation de données d’extension :

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Lorsque vous utilisez l’extension de message de protocole SAML, la réponse SAML ressemble à l’exemple suivant :

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Exiger des réponses SAML signées

Azure AD B2C exige la signature de toutes les assertions entrantes. Vous pouvez supprimer cette exigence en définissant la valeur de **WantsSignedAssertions** sur `false`. Dans ce cas, lesLe fournisseur d’identité ne doit pas signer les assertions mais, même s’il le fait, Azure AD B2C ne valide pas la signature.

La métadonnée **WantsSignedAssertions** contrôle l’indicateur de métadonnée SAML **WantsAssertionsSigned** inclus dans la métadonnée du profil technique Azure AD B2C partagé avec le fournisseur d’identité.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Si vous désactivez la validation des assertions, vous pouvez également désactiver la validation de signature de message de réponse. Définissez la métadonnée **ResponsesSigned** sur `false`. Dans ce cas, le fournisseur d’identité ne devrait pas signer le message de réponse SAML mais, même s’il le fait, Azure AD B2C ne validera pas la signature.

L’exemple suivant supprime tant le message que la signature d’assertion :

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Exiger des réponses SAML chiffrées

Pour exiger le chiffrement de toutes les assertions entrantes, définissez la métadonnée **WantsEncryptedAssertions** . Quand le chiffrement est requis, le fournisseur d’identité utilise une clé publique d’un certificat de chiffrement dans un profil technique Azure AD B2C. Azure AD B2C déchiffre l’assertion de réponse à l’aide de la partie privée du certificat de chiffrement.

Si vous activez le chiffrement des assertions, il se peut que vous deviez également désactiver la validation de signature de réponse (pour plus d’informations, consultez [Exiger des réponses SAML signées](#require-signed-saml-responses)).

Si la valeur de la métadonnée **WantsEncryptedAssertions **est**, la métadonnée du profil technique Azure AD B2C inclut la section** chiffrement`true`. Le fournisseur d’identité lit les métadonnées et chiffre l’assertion de réponse SAML avec la clé publique fournie dans les métadonnées du profil technique Azure AD B2C.

L’exemple suivant illustre la section Descripteur de clé des métadonnées SAML utilisées pour le chiffrement :

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Pour chiffrer l’assertion de réponse SAML :

1. [Créez une clé de stratégie](identity-provider-generic-saml.md#create-a-policy-key) avec un identificateur unique. Par exemple : `B2C_1A_SAMLEncryptionCert`.
2. Dans la collection **CryptographicKeys** de votre profil technique SAML. Définissez la valeur de **StorageReferenceId** sur le nom de la clé de stratégie que vous avez créée à la première étape. L’ID `SamlAssertionDecryption` indique l’utilisation de la clé de chiffrement pour chiffrer et déchiffrer l’assertion de la réponse SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Affectez la valeur `true` à l’élément de métadonnées de profil technique **WantsEncryptedAssertions**.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Mettez à jour le fournisseur d’identité avec la nouvelle métadonnée de profil technique Azure AD B2C. Vous devez voir le **KeyDescriptor** avec la propriété **use** définie sur `encryption` contenant la clé publique de votre certificat.

## <a name="enable-use-of-context-claims"></a>Activer l’utilisation des revendications de contexte

Dans la collection de revendications d’entrée et de sortie, vous pouvez inclure des revendications qui ne sont pas retournées par le fournisseur d’identité tant que vous définissez l’attribut `DefaultValue`. Vous pouvez également utiliser des [revendications de contexte](claim-resolver-overview.md) à inclure dans le profil technique. Pour utiliser une revendication de contexte :

1. Ajoutez un type de revendication à l’élément [ClaimsSchema](claimsschema.md) dans [BuildingBlocks](buildingblocks.md).
2. Ajoutez une revendication de sortie à la collection d’entrée ou de sortie. Dans l’exemple suivant, la première revendication définit la valeur du fournisseur d’identité. La deuxième revendication utilise les [revendications de contexte](claim-resolver-overview.md)d’adresse IP de l’utilisateur.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Désactiver une sortie unique

Lors d’une demande de déconnexion d’application, Azure AD B2C tente de se déconnecter de votre fournisseur d’identité SAML. Pour plus d’informations, consultez [Déconnexion d’une session Azure AD B2C](session-behavior.md#sign-out).  Pour désactiver le comportement de déconnexion, définissez la métadonnée **SingleLogoutEnabled** sur `false`.

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Déboguer le protocole SAML

Pour faciliter la configuration et le débogage de la fédération avec un fournisseur d’identité SAML, vous pouvez utiliser une extension de navigateur pour le protocole SAML, telle que l’[Extension SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pour Chrome, le [Traceur SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pour Firefox ou les [Outils de développement Edge ou IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Ces outils vous permettent de vérifier l’intégration entre Azure AD B2C et votre fournisseur d’identité SAML. Par exemple :

* Vérifiez si la demande SAML contient une signature, et déterminez l’algorithme utilisé pour la connexion à la demande d’autorisation.
* Récupérez les revendications (assertions) sous la section `AttributeStatement`.
* Vérifiez si le fournisseur d’identité retourne un message d’erreur.
* Vérifiez si la section d’assertion est chiffrée.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment diagnostiquer les problèmes liés à vos stratégies personnalisées à l’aide d’[Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
