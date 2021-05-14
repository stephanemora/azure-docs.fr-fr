---
title: Attributs de profil utilisateur dans Azure Active Directory B2C
description: Découvrez les attributs de type de ressource utilisateur pris en charge par le profil utilisateur du répertoire Azure AD B2C. Découvrez les attributs intégrés, les extensions et la façon dont les attributs sont mappés à Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 523212e0f63453e78967eb9a4716febb754ea5d6
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315270"
---
# <a name="user-profile-attributes"></a>Attributs de profil utilisateur

Le profil utilisateur de votre répertoire Azure Active Directory (Azure AD) B2C est fourni avec un ensemble intégré d’attributs, tels que le prénom, le nom, la ville, le code postal et le numéro de téléphone. Vous pouvez étendre le profil utilisateur avec vos propres données d’application sans avoir besoin d’un magasin de données externe.

La plupart des attributs qui peuvent être utilisés avec les profils utilisateur Azure AD B2C sont également pris en charge par Microsoft Graph. Cet article décrit les attributs de profil utilisateur Azure AD B2C pris en charge. Il présente également les attributs qui ne sont pas pris en charge par Microsoft Graph, ainsi que les attributs Microsoft Graph qui ne doivent pas être utilisés avec Azure AD B2C.

> [!IMPORTANT]
> Vous ne devez pas utiliser d’attributs intégrés ou d’extension pour stocker des données personnelles sensibles, telles que les informations d’identification de compte, les numéros d’identification de gouvernement, les données de titulaires de carte, les données de compte financier, les informations médicales ou les informations sensibles.

Vous pouvez également intégrer des systèmes externes. Par exemple, vous pouvez utiliser Azure AD B2C pour l’authentification, mais déléguer les données client à une base de données externe de gestion de la relation client (CRM) ou de fidélisation des clients en tant que source faisant autorité. Pour plus d’informations, consultez la solution [Profil distant](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

## <a name="azure-ad-user-resource-type"></a>Type de ressource utilisateur Azure AD

Le tableau ci-dessous répertorie les attributs de [type de ressource utilisateur](/graph/api/resources/user) pris en charge par le profil utilisateur du répertoire Azure AD B2C. Il fournit les informations suivantes sur chaque attribut :

- Nom d’attribut utilisé par Azure AD B2C (suivi du nom Microsoft Graph entre parenthèses, si différent)
- Type de données de l’attribut
- Description de l’attribut
- Si l’attribut est disponible dans le portail Azure
- Si l’attribut peut être utilisé dans un flux d’utilisateur
- Si l’attribut peut être utilisé dans une stratégie personnalisée [profil technique Azure AD](active-directory-technical-profile.md) et dans quelle section (&lt;InputClaims&gt;, &lt;OutputClaims&gt; ou &lt;PersistedClaims&gt;)

|Nom     |Type     |Description|Portail Azure|Flux d’utilisateurs|Stratégie personnalisée|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Si le compte d’utilisateur est activé ou désactivé : **true** si le compte est activé, sinon **false**.|Oui|Non|Persistant, Sortie|
|ageGroup        |String|Groupe d’âge de l’utilisateur. Valeurs possibles : null, Undefined, Minor, Adult, NotAdult.|Oui|Non|Persistant, sortie|
|alternativeSecurityId ([Identities](#identities-attribute))|String|Identité d’utilisateur unique du fournisseur d’identité externe.|Non|Non|Entrée, Persistant, Sortie|
|alternativeSecurityIds ([Identities](#identities-attribute))|Collection d’ID de sécurité alternatifs|Collection d’identités d’utilisateur provenant de fournisseurs d’identité externes.|Non|Non|Persistant, Sortie|
|city            |String|Localité où se trouve l’utilisateur. Longueur max. : 128.|Oui|Oui|Persistant, Sortie|
|consentProvidedForMinor|String|Indique si le consentement a été fourni pour un mineur. Valeurs autorisées : null, granted, denied, or notRequired.|Oui|Non|Persistant, Sortie|
|country         |String|Pays/région où l’utilisateur se trouve. Exemple : « US » ou « UK ». Longueur max. : 128.|Oui|Oui|Persistant, Sortie|
|createdDateTime|DateTime|Date de création de l’objet utilisateur. Lecture seule.|Non|Non|Persistant, Sortie|
|creationType    |String|Si le compte d’utilisateur a été créé en tant que compte local pour un locataire Azure Active Directory B2C, la valeur est LocalAccount ou nameCoexistence. Lecture seule.|Non|Non|Persistant, Sortie|
|dateOfBirth     |Date|Date de naissance.|Non|Non|Persistant, Sortie|
|department      |String|Nom du service où travaille l’utilisateur. Longueur max. : 64.|Oui|Non|Persistant, Sortie|
|displayName     |String|Nom d’affichage de l’utilisateur. Longueur max. : 256.|Oui|Oui|Persistant, Sortie|
|facsimileTelephoneNumber<sup>1</sup>|String|Numéro de téléphone de l’appareil fax professionnel de l’utilisateur.|Oui|Non|Persistant, Sortie|
|givenName       |String|Prénom de l’utilisateur. Longueur max. : 64.|Oui|Oui|Persistant, Sortie|
|jobTitle        |String|Intitulé du poste de l’utilisateur. Longueur max. : 128.|Oui|Oui|Persistant, Sortie|
|ImmutableID     |String|Identificateur généralement utilisé pour les utilisateurs migrés à partir de Windows Server Active Directory.|Non|Non|Persistant, Sortie|
|legalAgeGroupClassification|String|Classification de tranche d’âge légal. En lecture seule et calculée en fonction des propriétés ageGroup et consentProvidedForMinor. Valeurs autorisées : null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult et adult.|Oui|Non|Persistant, Sortie|
|legalCountry<sup>1</sup>  |String|Pays/région à des fins juridiques.|Non|Non|Persistant, Sortie|
|mailNickName    |String|Alias de messagerie de l’utilisateur. Longueur max. : 64.|Non|Non|Persistant, Sortie|
|mobile (mobilePhone) |String|Numéro de portable principal de l’utilisateur. Longueur max. : 64.|Oui|Non|Persistant, Sortie|
|netId           |String|ID Net.|Non|Non|Persistant, Sortie|
|objectId        |String|Identificateur global unique (GUID) qui est l’identificateur unique de l’utilisateur. Exemple : 12345678-9abc-def0-1234-56789abcde. Lecture seule, non modifiable.|Lecture seule|Oui|Entrée, Persistant, Sortie|
|otherMails      |Collection de chaînes|Liste d’autres adresses e-mail pour l’utilisateur. Exemple: ["bob@contoso.com", "Robert@fabrikam.com"]. REMARQUE : Les caractères accentués ne sont pas autorisés.|Oui (adresse e-mail secondaire)|Non|Persistant, Sortie|
|mot de passe        |String|Mot de passe du compte local lors de la création de l’utilisateur.|Non|Non|Persistant|
|passwordPolicies     |String|Stratégie du passe du compte. Il s’agit d’une chaîne composée d’un nom de stratégie différent, séparé par une virgule. Par exemple : « DisablePasswordExpiration, DisableStrongPassword ».|Non|Non|Persistant, Sortie|
|physicalDeliveryOfficeName (officeLocation)|String|Emplacement du bureau de l’utilisateur. Longueur max. : 128.|Oui|Non|Persistant, Sortie|
|postalCode      |String|Code postal correspondant à l’adresse postale de l’utilisateur. Le code postal est spécifique au pays ou à la région de l’utilisateur. Aux États-Unis, cet attribut contient le code ZIP. Longueur max. : 40.|Oui|Non|Persistant, Sortie|
|preferredLanguage    |String|Langue préférée pour l’utilisateur. Le format de langue par défaut est basé sur la norme RFC 4646. Le nom est la combinaison entre un code de culture à deux lettres minuscules ISO 639 associé à la langue et un code de sous-culture à deux lettres majuscules ISO 3166 associé au pays ou à la région. Exemples : « fr-FR », « es-ES ».|Non|Non|Persistant, Sortie|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Les jetons d’actualisation émis avant cet instant ne sont pas valides et les applications reçoivent une erreur lors de l’utilisation d’un jeton d’actualisation non valide pour acquérir un nouveau jeton d’accès. Dans ce cas, l’application doit acquérir un nouveau jeton d’actualisation en effectuant une demande au point de terminaison d’autorisation. Lecture seule.|Non|Non|Output|
|signInNames ([Identities](#identities-attribute)) |String|Nom de connexion unique de l’utilisateur de compte local de tout type dans le répertoire. Utilisez cet attribut pour obtenir un utilisateur avec une valeur de connexion sans spécifier le type de compte local.|Non|Non|Entrée|
|signInNames.userName ([Identities](#identities-attribute)) |String|Nom d’utilisateur unique de l’utilisateur du compte local dans le répertoire. Utilisez cet attribut pour créer ou obtenir un utilisateur avec un nom d’utilisateur de connexion spécifique. Si vous spécifiez cela dans PersistedClaims seul pendant l’opération Patch, les autres types de signInNames sont supprimés. Si vous souhaitez ajouter un nouveau type de signInNames, vous devez également conserver les signInNames existants. REMARQUE : Les caractères accentués ne sont pas autorisés dans le nom d’utilisateur.|Non|Non|Entrée, Persistant, Sortie|
|signInNames.phoneNumber ([Identities](#identities-attribute)) |String|Numéro de téléphone unique de l’utilisateur du compte local dans le répertoire. Utilisez cet attribut pour créer ou obtenir un utilisateur avec un numéro de téléphone de connexion spécifique. Si vous spécifiez cet attribut dans PersistedClaims seul pendant l’opération Patch, les autres types de signInNames sont supprimés. Si vous souhaitez ajouter un nouveau type de signInNames, vous devez également conserver les signInNames existants.|Non|Non|Entrée, Persistant, Sortie|
|signInNames.emailAddress ([Identities](#identities-attribute))|String|Adresse e-mail unique de l’utilisateur du compte local dans le répertoire. Utilisez ceci fonction pour créer ou obtenir une adresse e-mail de connexion propre à un utilisateur. Si vous spécifiez cet attribut dans PersistedClaims seul pendant l’opération Patch, les autres types de signInNames sont supprimés. Si vous souhaitez ajouter un nouveau type de signInNames, vous devez également conserver les signInNames existants.|Non|Non|Entrée, Persistant, Sortie|
|state           |String|État ou province dans l’adresse de l’utilisateur. Longueur max. : 128.|Oui|Oui|Persistant, Sortie|
|streetAddress   |String|Adresse postale du lieu de travail de l’utilisateur. Longueur max. : 1024.|Oui|Oui|Persistant, Sortie|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Numéro de téléphone secondaire de l’utilisateur, utilisé pour l’authentification multifacteur.|Oui|Non|Persistant, Sortie|
|strongAuthenticationEmailAddress<sup>1</sup>|String|Adresse SMTP de l’utilisateur. Exemple : « bob@contoso.com » Cet attribut est utilisé pour la connexion avec la stratégie de nom d’utilisateur, pour stocker l’adresse e-mail de l’utilisateur. L’adresse e-mail est ensuite utilisée dans un flow de réinitialisation de mot de passe. Les caractères accentués ne sont pas autorisés dans cet attribut.|Oui|Non|Persistant, Sortie|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|Numéro de téléphone principal de l’utilisateur, utilisé pour l’authentification multifacteur.|Oui|Non|Persistant, Sortie|
|surname         |String|Patronyme de l’utilisateur (nom). Longueur max. : 64.|Oui|Oui|Persistant, Sortie|
|telephoneNumber (première entrée de businessPhones)|String|Numéro de téléphone principal du lieu de travail de l’utilisateur.|Oui|Non|Persistant, Sortie|
|userPrincipalName    |String|Nom d’utilisateur principal (UPN) de l’utilisateur. L’UPN est un nom de connexion au format Internet d’un utilisateur selon la norme Internet RFC 822. Le domaine doit être présent dans la collection de domaines vérifiés du locataire. Cette propriété est obligatoire quand un compte est créé. Non modifiable.|Non|Non|Entrée, Persistant, Sortie|
|usageLocation   |String|Requis pour les utilisateurs auxquels des licences seront attribuées en raison des exigences légales sur la vérification de la disponibilité des services dans les pays/régions. N'accepte pas la valeur NULL. Code de pays/région à deux lettres (norme ISO 3166). Exemples : « US », « JP » et « GB ».|Oui|Non|Persistant, Sortie|
|userType        |String|Valeur de chaîne qui permet de classer des types d'utilisateurs dans votre répertoire. La valeur doit être Member. Lecture seule.|Lecture seule|Non|Persistant, Sortie|
|userState (externalUserState)<sup>3</sup>|String|Pour un compte Azure AD B2B uniquement, indique si le statut de l’invitation est PendingAcceptance (En attente d’acceptation) ou Accepted (Acceptée).|Non|Non|Persistant, Sortie|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Affiche l’horodatage de la modification la plus récente de la propriété UserState.|Non|Non|Persistant, Sortie|

<sup>1</sup>Non pris en charge par Microsoft Graph<br><sup>2</sup> Pour plus d’informations, consultez [Attribut de numéro de téléphone MFA](#mfa-phone-number-attribute)<br><sup>3</sup> Ne doit pas être utilisé avec Azure AD B2C

## <a name="display-name-attribute"></a>Attribut de nom d’affichage

`displayName` est le nom à afficher dans la gestion des utilisateurs du portail Azure pour l’utilisateur et dans le jeton d’accès qu’Azure AD B2C retourne à l’application. Cette propriété est requise.

## <a name="identities-attribute"></a>Attribut d’identités

Un compte de client, qui peut être un consommateur, un partenaire ou un citoyen, peut être associé à ces types d’identité :

- Identité **locale** : Le nom d’utilisateur et le mot de passe sont stockés localement dans l’annuaire Azure AD B2C. Nous faisons souvent référence à ces identités en parlant de « comptes locaux ».
- Identités **fédérées** : Également appelées comptes *sociaux* ou d’*entreprise*. L’identité de l’utilisateur est managée par un fournisseur d’identité fédéré, comme Facebook, Microsoft, ADFS ou Salesforce.

Un utilisateur disposant d’un compte client peut se connecter avec plusieurs identités. Par exemple, le nom d’utilisateur, l’adresse e-mail, l’ID d’employé, l’ID national, etc. Un même compte peut avoir plusieurs identités, à la fois locales et de réseaux sociaux, avec le même mot de passe.

Dans l’API Microsoft Graph, les identités locales et fédérées sont stockées dans l’attribut `identities` de l’utilisateur, qui est de type [objectIdentity](/graph/api/resources/objectidentity). La collection `identities` représente un ensemble d’identités utilisées pour se connecter à un compte d’utilisateur. Cette collection permet à l’utilisateur de se connecter au compte d’utilisateur avec une de ses identités associées. L’attribut d’identités peut contenir jusqu’à dix objets [objectIdentity](/graph/api/resources/objectidentity) . Chaque objet contient les propriétés suivantes :

| Nom   | Type |Description|
|:---------------|:--------|:----------|
|signInType|string| Spécifie les types de connexion utilisateur dans votre annuaire. Pour le compte local : `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName` ou tout autre type de votre choix. Le compte social doit être défini sur `federated`.|
|émetteur|string|Spécifie l’émetteur de l’identité. Pour les comptes locaux (où  **signInType** n’est pas `federated`), cette propriété est le nom de domaine par défaut du locataire B2C local, par exemple `contoso.onmicrosoft.com`. Pour l’identité de réseaux sociaux (où **signInType** est `federated`), la valeur est le nom de l’émetteur, par exemple `facebook.com`|
|issuerAssignedId|string|Spécifie l’identificateur unique affecté à l’utilisateur par l’émetteur. La combinaison de **issuer** et **issuerAssignedId** doit être unique au sein de votre locataire. Pour un compte local, quand **signInType** est défini sur `emailAddress` ou sur `userName`, il représente le nom de connexion pour l’utilisateur.<br>Quand **signInType** est défini sur : <ul><li>`emailAddress` (ou commence par `emailAddress`, comme `emailAddress1`), **issuerAssignedId** doit être une adresse e-mail valide</li><li>`userName` (ou toute autre valeur), **issuerAssignedId** doit être une [partie locale valide d’une adresse e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** représente l’identificateur unique du compte fédéré</li></ul>|

L’attribut **Identities** suivant, avec une identité de compte local comprenant un nom de connexion, une adresse e-mail comme identifiant de connexion et une identité de réseau social.

```json
"identities": [
  {
    "signInType": "userName",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "johnsmith"
  },
  {
    "signInType": "emailAddress",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "jsmith@yahoo.com"
  },
  {
    "signInType": "federated",
    "issuer": "facebook.com",
    "issuerAssignedId": "5eecb0cd"
  }
]
```

Pour les identités fédérées, en fonction du fournisseur d’identité, **issuerAssignedId** est une valeur unique pour un utilisateur donné par application ou par compte de développement. Configurez la stratégie Azure AD B2C avec le même ID d’application que celui précédemment attribué par le fournisseur d’identité sociale ou une autre application au sein du même compte de développement.

## <a name="password-profile-property"></a>Propriété de profil de mot de passe

Pour une identité locale, l’attribut **passwordProfile** est obligatoire et contient le mot de passe de l’utilisateur. L’attribut `forceChangePasswordNextSignIn` indique si un utilisateur doit réinitialiser le mot de passe lors de la prochaine connexion. Pour gérer une réinitialisation forcée du mot de passe, [configurez le flux de réinitialisation forcée du mot de passe](force-password-reset.md).

Pour une identité (de réseau social) fédérée, l’attribut **passwordProfile** n’est pas nécessaire.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Attribut de stratégie de mot de passe

La stratégie de mot de passe Azure AD B2C (pour les comptes locaux) est basée sur la stratégie de [force de mot de passe fort](../active-directory/authentication/concept-sspr-policy.md) d’Azure Active Directory. Les stratégies de réinitialisation du mot de passe, d’inscription ou de connexion Azure AD B2C utilisent des mots de passe forts et qui n’expirent pas.

Dans les scénarios de migration d’utilisateurs, si les comptes que vous voulez migrer ont un mot de passe moins fort que les [règles de mot de passe fort](../active-directory/authentication/concept-sspr-policy.md) d’Azure AD B2C, vous pouvez désactiver l’exigence d’un mot de passe fort. Pour modifier la stratégie de mot de passe par défaut, définissez l’attribut `passwordPolicies` sur `DisableStrongPassword`. Par exemple, vous pouvez modifier la demande de création d’utilisateur comme suit :

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Attribut de numéro de téléphone MFA

Lorsque vous utilisez un téléphone pour l’authentification multifacteur (MFA), le téléphone mobile est utilisé pour vérifier l’identité de l’utilisateur. Pour [ajouter](/graph/api/authentication-post-phonemethods) un nouveau numéro de téléphone par programmation, [mettez à jour](/graph/api/b2cauthenticationmethodspolicy-update), [obtenez](/graph/api/b2cauthenticationmethodspolicy-get) ou [supprimez](/graph/api/phoneauthenticationmethod-delete) le numéro de téléphone, et utilisez la [méthode d’authentification par téléphone](/graph/api/resources/phoneauthenticationmethod) de l’API MS Graph.

Dans les [stratégies personnalisées](custom-policy-overview.md) d’Azure AD B2C, le numéro de téléphone est disponible via le type de revendication `strongAuthenticationPhoneNumber`.

## <a name="extension-attributes"></a>Attributs d’extension

Toutes les applications orientées client ont des exigences uniques concernant les informations à recueillir. Votre locataire Azure AD B2C est fourni avec un ensemble intégré d’informations stockées dans des propriétés, comme le prénom, le nom et le code postal. Avec Azure AD B2C, vous pouvez étendre l’ensemble de propriétés stockées dans chaque compte client. Pour plus d’informations, consultez [Ajouter des attributs utilisateur et personnaliser l’entrée utilisateur dans Azure Active Directory B2C](configure-user-input.md).

Les attributs d’extension [étendent le schéma](/graph/extensibility-overview#schema-extensions) des objets utilisateur dans le répertoire. Les attributs d’extension ne peuvent être inscrits que pour un objet application, même s’ils peuvent contenir les données d’un utilisateur. L’attribut d’extension est attaché à l’application appelée `b2c-extensions-app`. Ne modifiez pas cette application, car elle est utilisée par Azure AD B2C pour le stockage des données utilisateurs. Vous trouverez cette application sous les inscriptions d’applications Azure Active Directory.

> [!NOTE]
> - Jusqu’à 100 attributs d’extension peuvent être écrits dans un compte d’utilisateur donné.
> - Si l’application b2c-extensions-app est supprimée, ces attributs d’extension sont supprimés de tous les utilisateurs, ainsi que toutes les données qu’ils contiennent.
> - Si un attribut d’extension est supprimé par l’application, il est supprimé de tous les comptes d’utilisateur et les valeurs sont supprimées.

Les attributs d’extension dans l’API Graph sont nommés d’après la convention `extension_ApplicationClientID_AttributeName`, où `ApplicationClientID` est l’**ID d’application (client)** de l’application `b2c-extensions-app` (accessible dans **Inscriptions d’applications** > **Toutes les applications** dans le portail Azure). Notez que l’**ID d’application (client)** tel qu’il est représenté dans le nom de l’attribut d’extension ne comprend aucun trait d’union. Par exemple :

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Les types de données suivants sont pris en charge lors de la définition d’un attribut dans une extension de schéma :

|Type |Notes  |
|--------------|---------|
|Boolean    | Valeurs possibles : **True** ou **False**. |
|DateTime   | Doit être spécifié au format ISO 8601. Est stocké au format UTC.   |
|Integer    | Valeur 32 bits.               |
|String     | 256 caractères maximum.     |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les attributs d’extension :

- [Extensions de schéma](/graph/extensibility-overview#schema-extensions)
- [Définir des attributs personnalisés](user-flow-custom-attributes.md)
