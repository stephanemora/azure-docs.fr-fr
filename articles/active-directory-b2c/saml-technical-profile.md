---
title: Définir un profil technique SAML dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définir un profil technique SAML dans une stratégie personnalisée dans Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74666b1dc2ba4fac25aff0a56a52d048d746d465
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950933"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique SAML dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) prend en charge le fournisseur d’identité SAML 2.0. Cet article décrit les caractéristiques d’un profil technique permettant d’interagir avec un fournisseur de revendications qui prend en charge ce protocole normalisé. Un profil technique SAML vous permet d’opérer une fédération avec un fournisseur d’identité basée sur SAML, tel que [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) et [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Une telle fédération permet à vos utilisateurs de se connecter avec leurs identités existantes de réseaux sociaux ou d’entreprise.

## <a name="metadata-exchange"></a>Échange de métadonnées

Les métadonnées sont des informations utilisées dans le protocole SAML pour exposer la configuration d’une partie SAML, telle qu’un fournisseur de services ou un fournisseur d’identité. Elles définissent l’emplacement des services, tels que la connexion et la déconnexion, les certificats, la méthode de connexion, et bien plus encore. Le fournisseur d’identité utilise les métadonnées pour savoir comment communiquer avec Azure AD B2C. Les métadonnées sont configurées au format XML et peuvent être signées avec une signature numérique afin que l’autre partie puisse valider son intégrité. Quand Azure AD B2C fédère avec un fournisseur d’identité SAML, il agit en tant que fournisseur de services lançant une demande SAML et attendant une réponse SAML. Et, dans certains cas, il accepte l’authentification SAML non sollicitée (également appelé « flux lancé par le fournisseur d’identité »).

Les métadonnées peuvent être configurées dans les deux parties en tant que « Métadonnées statiques » ou « Métadonnées dynamiques ». En mode statique, vous copiez l’ensemble des métadonnées à partir d’une partie et vous les définissez dans l’autre partie. En mode dynamique, vous définissez l’URL des métadonnées tandis que l’autre partie lit la configuration de façon dynamique. Les principes sont les mêmes : vous définissez les métadonnées du profil technique Azure AD B2C dans votre fournisseur d’identité et les métadonnées du fournisseur d’identité dans Azure AD B2C.

Chaque fournisseur d’identité SAML a différentes étapes pour exposer et définir le fournisseur de services, dans le cas présent Azure AD B2C, et pour définir les métadonnées Azure AD B2C dans le fournisseur d’identité. Pour obtenir des conseils sur la marche à suivre, consultez la documentation de votre fournisseur d’identité.

L’exemple suivant montre une adresse URL aux métadonnées SAML d’un profil technique d’AAD B2C :

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Remplacez les valeurs suivantes :

- **your-tenant-name** par le nom de votre locataire, par exemple fabrikam.b2clogin.com.
- **your-policy** par le nom de votre stratégie. Utilisez la stratégie dans laquelle vous configurez le profil technique du fournisseur SAML, ou une stratégie qui hérite de cette stratégie.
- **your-technical-profile** par le nom du profil technique de votre fournisseur d’identité SAML.

## <a name="digital-signing-certificates-exchange"></a>Échange de certificats de signature numérique

Pour créer une approbation entre Azure AD B2C et votre fournisseur d’identité SAML, vous devez fournir un certificat X509 valide avec la clé privée. Vous chargez le certificat avec la clé privée (fichier .pfx) dans le magasin de clés de la stratégie Azure AD B2C. Azure AD B2C signe numériquement la demande de connexion SAML à l’aide du certificat que vous fournissez.

Le certificat est utilisé comme suit :

- Azure AD B2C génère et signe une demande SAML, à l’aide de la clé privée Azure AD B2C du certificat. La demande SAML est envoyée au fournisseur d’identité, qui la valide à l’aide de la clé publique Azure AD B2C du certificat. Le certificat public Azure AD B2C est accessible par le biais des métadonnées du profil technique. En guise d’alternative, vous pouvez charger manuellement le fichier .cer vers votre fournisseur d’identité SAML.
- Le fournisseur d’identité signe les données envoyées à Azure AD B2C à l’aide de la clé privée du certificat du fournisseur d’identité. Azure AD B2C valide les données à l’aide du certificat public du fournisseur d’identité. Chaque fournisseur d’identité met en place différentes étapes pour la configuration. Pour obtenir des conseils sur la marche à suivre, consultez la documentation de votre fournisseur d’identité. Dans Azure AD B2C, votre stratégie doit pouvoir accéder à la clé publique du certificat à l’aide des métadonnées du fournisseur d’identité.

Un certificat auto-signé est acceptable pour la plupart des scénarios. Pour les environnements de production, nous vous recommandons d’utiliser un certificat X509 émis par une autorité de certification. En outre, comme décrit plus loin dans ce document, pour un environnement hors production, vous pouvez désactiver la signature SAML des deux côtés.

Le diagramme suivant illustre l’échange de métadonnées et de certificat :

![échange de métadonnées et de certificat](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Chiffrement numérique

Pour chiffrer l’assertion de réponse SAML, le fournisseur d’identité utilise toujours une clé publique d’un certificat de chiffrement dans un profil technique Azure AD B2C. Quand Azure AD B2C doit déchiffrer les données, il utilise la partie privée du certificat de chiffrement.

Pour chiffrer l’assertion de réponse SAML :

1. Chargez un certificat X509 valide avec la clé privée (fichier .pfx) vers le magasin de clés de la stratégie Azure AD B2C.
2. Ajoutez un élément **CryptographicKey** avec `SamlAssertionDecryption` comme identificateur à la collection **CryptographicKeys** du profil technique. Affectez le nom de la clé de stratégie que vous avez créée à l’étape 1 comme valeur de **StorageReferenceId**.
3. Affectez la valeur `true` à l’élément de métadonnées de profil technique **WantsEncryptedAssertions**.
4. Mettez à jour le fournisseur d’identité avec les nouvelles métadonnées de profil technique Azure AD B2C. Vous devez voir le **KeyDescriptor** avec la propriété **use** définie sur `encryption` contenant la clé publique de votre certificat.

L’exemple suivant montre la section de chiffrement de profil technique Azure AD B2C des métadonnées :

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément Protocol doit être défini sur `SAML2`.

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications retournées par le fournisseur d’identité SAML dans la section `AttributeStatement`. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d'identité. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut `DefaultValue`.

Pour lire l’assertion SAML **NamedId** dans **Objet** comme une revendication normalisée, définissez la revendication **PartnerClaimType** sur `assertionSubjectName`. Assurez-vous que **NameId** est la première valeur dans l’assertion XML. Lorsque vous définissez plusieurs assertion, Azure AD B2C sélectionne la valeur d’objet de la dernière assertion.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre les revendications retournées par le fournisseur d’identité Facebook :

- La revendication **issuerUserId** est mappée à la revendication **assertionSubjectName**.
- Revendication **first_name** mappée à la revendication **givenName**.
- Revendication **last_name** mappée à la revendication **surname**.
- Revendication **displayName** sans mappage de nom.
- La revendication **email** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité :

- La revendication **identityProvider** contenant le nom du fournisseur d’identité.
- La revendication **authenticationSource** avec la valeur par défaut **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| PartnerEntity | OUI | URL des métadonnées du fournisseur d’identité SAML. Copiez les métadonnées du fournisseur d’identité et ajoutez-les à l’intérieur de l’élément CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Non | Indique si le profil technique exige que toutes les demandes d’authentification sortantes soient signées. Valeurs possibles : `true` ou `false`. La valeur par défaut est `true`. Quand la valeur est `true`, la clé de chiffrement **SamlMessageSigning** doit être spécifiée et toutes les demandes d’authentification sortantes sont signées. Si la valeur est `false`, les paramètres **SigAlg** et **Signature** (chaîne de requête ou paramètre d’envoi) sont omis de la demande. Ces métadonnées contrôlent également l’attribut **AuthnRequestsSigned** des métadonnées, qui est généré dans les métadonnées du profil technique Azure AD B2C partagées avec le fournisseur d’identité. Azure AD B2C ne signe pas la demande si la valeur de **WantsSignedRequests** dans les métadonnées du profil technique est définie sur `false` et si les métadonnées du fournisseur identité **WantAuthnRequestsSigned** sont définies sur `false` ou ne sont pas spécifiées. |
| XmlSignatureAlgorithm | Non | Méthode utilisée par Azure AD B2C pour signer la demande SAML. Ces métadonnées contrôlent la valeur du paramètre **SigAlg** (chaîne de requête ou paramètre d’envoi) dans la demande SAML. Valeurs possibles : `Sha256`, `Sha384`, `Sha512` ou `Sha1`. Veillez à configurer l’algorithme de signature des deux côtés avec la même valeur. Utilisez uniquement l’algorithme pris en charge par votre certificat. |
| WantsSignedAssertions | Non | Indique si le profil technique exige que toutes les assertions entrantes soient signées. Valeurs possibles : `true` ou `false`. La valeur par défaut est `true`. Si la valeur est `true`, toutes les sections d’assertions `saml:Assertion` envoyées par le fournisseur d’identité à Azure AD B2C doivent être signées. Si la valeur est `false`, le fournisseur d’identité ne doit pas signer les assertions, mais même s’il le fait, Azure AD B2C ne validera pas la signature. Ces métadonnées contrôlent également l’indicateur de métadonnées **WantsAssertionsSigned**, qui est généré dans les métadonnées du profil technique Azure AD B2C partagées avec le fournisseur d’identité. Si vous désactivez la validation des assertions, vous pouvez également désactiver la validation de signature de réponse (pour plus d’informations, consultez **ResponsesSigned**). |
| ResponsesSigned | Non | Valeurs possibles : `true` ou `false`. La valeur par défaut est `true`. Si la valeur est `false`, le fournisseur d’identité ne doit pas signer la réponse SAML, mais même s’il le fait, Azure AD B2C ne validera pas la signature. Si la valeur est `true`, la réponse SAML envoyée par le fournisseur d’identité à Azure AD B2C est signée et doit être validée. Si vous désactivez la validation de la réponse SAML, vous pouvez également désactiver la validation de signature d’assertion (pour plus d’informations, consultez **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Non | Indique si le profil technique exige que toutes les assertions entrantes soient chiffrées. Valeurs possibles : `true` ou `false`. La valeur par défaut est `false`. Si la valeur est `true`, les assertions envoyées par le fournisseur d’identité à Azure AD B2C doivent être signées et la clé de chiffrement **SamlAssertionDecryption** doit être spécifiée. Si la valeur est `true`, les métadonnées du profil technique Azure AD B2C incluent la section **encryption**. Le fournisseur d’identité lit les métadonnées et chiffre l’assertion de réponse SAML avec la clé publique fournie dans les métadonnées du profil technique Azure AD B2C. Si vous activez le chiffrement des assertions, vous devrez peut-être également désactiver la validation de signature de réponse (pour plus d’informations, consultez **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Non | Indique si un profil de session d’authentification unique lancé par un profil de fournisseur d’identité SAML est activé. Valeurs possibles : `true` ou `false`. Par défaut, il s’agit de `false`. Dans le flux lancé par le fournisseur d’identité, l’utilisateur est authentifié en externe et une réponse non sollicitée est envoyée à Azure AD B2C, qui consomme le jeton, exécute les étapes d’orchestration, puis envoie une réponse à l’application par partie de confiance. |
| NameIdPolicyFormat | Non | Spécifie les contraintes appliquées à l’identificateur de nom à utiliser pour représenter l’objet demandé. En cas d’omission, tout type d’identificateur pris en charge par le fournisseur d’identité pour l’objet demandé peut être utilisé. Par exemple : `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** peut être utilisé avec **NameIdPolicyAllowCreate**. Pour obtenir des conseils concernant les stratégies d’identificateur de nom prises en charge, consultez la documentation de votre fournisseur d’identité. |
| NameIdPolicyAllowCreate | Non | Lorsque vous utilisez **NameIdPolicyFormat**, vous pouvez également spécifier la propriété `AllowCreate` de **NameIDPolicy**. La valeur de ces métadonnées est `true` ou `false` pour indiquer si le fournisseur d’identité est autorisé à créer un compte pendant le flux de connexion. Pour obtenir des conseils sur la marche à suivre, consultez la documentation de votre fournisseur d’identité. |
| AuthenticationRequestExtensions | Non | Éléments d’extension de message de protocole facultatifs convenus entre Azure AD BC et le fournisseur d’identité. L’extension est présentée au format XML. Vous ajoutez les données XML dans l’élément CDATA `<![CDATA[Your IDP metadata]]>`. Pour voir si l’élément d’extensions est pris en charge, consultez la documentation de votre fournisseur d’identité. |
| IncludeAuthnContextClassReferences | Non | Spécifie une ou plusieurs références d’URI identifiant des classes de contexte d’authentification. Par exemple, pour autoriser un utilisateur à se connecter uniquement avec un nom d’utilisateur et un mot de passe, définissez la valeur sur `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Pour autoriser la connexion avec nom d’utilisateur et mot de passe sur une session protégée (SSL/TLS), spécifiez `PasswordProtectedTransport`. Pour obtenir des conseils concernant les URI **AuthnContextClassRef** pris en charge, consultez la documentation de votre fournisseur d’identité. Spécifiez plusieurs URI sous la forme d’une liste délimitée par des virgules. |
| IncludeKeyInfo | Non | Indique si la demande d’authentification SAML contient la clé publique ou le certificat lorsque la liaison est définie sur `HTTP-POST`. Valeurs possibles : `true` ou `false`. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** contient les attributs suivants :

| Attribut |Obligatoire | Description |
| --------- | ----------- | ----------- |
| SamlMessageSigning |OUI | Certificat X509 (jeu de clés RSA) à utiliser pour signer les messages SAML. Azure AD B2C utilise cette clé pour signer les demandes et les envoyer au fournisseur d’identité. |
| SamlAssertionDecryption |OUI | Certificat X509 (jeu de clés RSA) à utiliser pour déchiffrer les messages SAML. Ce certificat doit être fourni par le fournisseur d’identité. Azure AD B2C utilise ce certificat pour déchiffrer les données envoyées par le fournisseur d’identité. |
| MetadataSigning |Non | Certificat X509 (jeu de clés RSA) à utiliser pour signer les métadonnées SAML. Azure AD B2C utilise cette clé pour signer les métadonnées.  |

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour obtenir des exemples d’utilisation des fournisseurs d’identité SAML dans Azure AD B2C :

- [Ajouter ADFS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Se connecter à l’aide de comptes Salesforce via SAML](active-directory-b2c-setup-sf-app-custom.md)
