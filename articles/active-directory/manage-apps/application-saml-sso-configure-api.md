---
title: Utiliser les API Microsoft Graph pour configurer l’authentification unique SAML
titleSuffix: Azure Active Directory
description: Vous devez configurer l’authentification unique SAML de plusieurs instances d’une application ? Découvrez comment gagner du temps en utilisant les API Microsoft Graph pour automatiser la configuration de l’authentification unique SAML.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50ee9e3c22c885931e2586f65ba2fa3353fccfeb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85355843"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatiser la configuration de l’application basée sur l’authentification unique SAML avec l’API Microsoft Graph

Dans cet article, vous allez apprendre à créer et à configurer une application à partir de la galerie Azure Active Directory (Azure AD). Cet article utilise AWS comme exemple, mais vous pouvez utiliser les étapes de cet article pour n’importe quelle application SAML dans la galerie Azure AD.

**Procédure d’utilisation des API Microsoft Graph pour automatiser la configuration de l’authentification unique SAML**

| Étape  | Détails  |
|---------|---------|
| [1. Créer l’application de galerie](#step-1-create-the-gallery-application) | Se connecter au client de l’API <br> Récupérer l’application de galerie <br> Créer l’application de galerie|
| [2. Configurer l’authentification unique](#step-2-configure-single-sign-on) | Récupérer l’ID d’objet de l’application et l’ID d’objet du principal du service <br> Définir le mode d'authentification unique <br> Définir des URL SAML de base telles que l’identificateur, l’URL de réponse et l’URL de connexion <br> Ajouter des rôles d’application (facultatif)|
| [3. Configurer le mappage de revendications](#step-3-configure-claims-mapping) | Créer une stratégie de mappage de revendications <br> Attribuer une stratégie de mappage de revendications au principal du service|
| [4. Configurer un certificat de signature](#step-4-configure-signing-certificate) | Créer un certificat <BR> Ajouter une clé de signature personnalisée <br> Activer la clé de signature personnalisée|
| [5. Affecter des utilisateurs](#step-5-assign-users) | Affecter des utilisateurs et des groupes à l’application
| [6. Configurer du côté de l'application](#step-6-configure-the-application-side)| Obtenir des métadonnées SAML Azure AD

**Liste de toutes les API utilisées dans la documentation**

Vérifiez que vous disposez des autorisations correspondantes pour appeler les API suivantes.

|Type de ressource |Méthode |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[Répertorier applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instancier applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Mettre à jour servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Créer appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Affecter claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[applications](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Mettre à jour une application](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Créer claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Les objets de réponse présentés dans cet article peuvent être raccourcis afin d’améliorer la lisibilité. Toutes les propriétés seront retournées à partir d’un appel réel.

## <a name="step-1-create-the-gallery-application"></a>Étape 1 : Créer l’application de galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Se connecter à l’afficheur Microsoft Graph (recommandé), à Postman ou à tout autre client d’API que vous utilisez

1. Lancer l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
2. Cliquez sur **Se connecter avec Microsoft** et connectez-vous à l’aide des informations d’identification d’administrateur de l’application ou d’administrateur global d’Azure AD.
3. Une fois connecté, les détails du compte d’utilisateur apparaissent dans le volet de gauche.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Récupérer l’identificateur du modèle de l’application de galerie

Les applications de la galerie d’applications Azure AD disposent toutes d’un [modèle d’application](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) qui décrit les métadonnées de cette dernière. À l’aide de ce modèle, vous pouvez créer une instance de l’application et du principal de service dans votre locataire à des fins de gestion.

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Créer l’application de galerie

À l’aide de l’ID de modèle que vous avez récupéré pour votre application lors de la dernière étape, [créez une instance](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) de l’application et du principal du service dans votre locataire.

> [!NOTE] 
> Vous pouvez utiliser l’API applicationTemplate pour instancier des [applications hors galerie](add-non-gallery-app.md). Utilisez applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621`.
#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>response


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Étape 2 : Configurer l’authentification unique

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Récupérer l’ID d’objet de l’application et l’ID d’objet du principal du service

Utilisez la réponse de l’appel précédent pour récupérer et enregistrer l’ID d’objet de l’application et l’ID d’objet du principal du service.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Définir le mode d'authentification unique

Dans cet exemple, vous allez définir `saml` comme mode d’authentification unique dans le [type de ressource servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Les autres propriétés d’authentification unique SAML que vous pouvez configurer sont les suivantes : `notificationEmailAddresses`, `loginUrl` et `samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Définir des URL SAML de base telles que l’identificateur, l’URL de réponse et l’URL de connexion

Définissez l’identificateur et les URL de réponse pour AWS dans l’objet d’application.

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Ajouter des rôles d’application (facultatif)

Si l’application requiert les informations de rôle dans le jeton, ajoutez la définition des rôles dans l’objet d’application. Pour AWS, vous pouvez [activer l’approvisionnement des utilisateurs](../app-provisioning/application-provisioning-configure-api.md) pour récupérer tous les rôles à partir de ce compte AWS. 

Pour plus d’informations, consultez [Configurer les revendications de rôle émises dans le jeton SAML](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Lorsque vous ajoutez des rôles d’application, ne modifiez pas les rôles d’application par défaut msiam_access. 

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Étape 3 : Configurer le mappage de revendications

### <a name="create-claims-mapping-policy"></a>Créer une stratégie de mappage de revendications

Outre les revendications de base, configurez les revendications suivantes pour Azure AD à émettre dans le jeton SAML :

| Nom de revendication | Source  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userPrincipalName |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | « 900 » |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userPrincipalName |

Pour plus d’informations, voir [Personnaliser des revendications émises dans des jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Attribuer une stratégie de mappage de revendications au principal du service

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Étape 4 : Configurer un certificat de signature

L’utilisation de l’API [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) ne crée pas de certificat de signature par défaut. Créez votre certificat de signature personnalisé et attribuez-le à l’application. 

### <a name="create-a-custom-signing-certificate"></a>Créer un certificat de signature personnalisé

Pour tester, vous pouvez utiliser la commande PowerShell suivante pour obtenir un certificat auto-signé. Utilisez les meilleures pratiques de sécurité de votre entreprise pour créer un certificat de signature pour la production.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Ajouter une clé de signature personnalisée

Ajoutez les informations suivantes au principal du service :

* Clé privée
* Mot de passe
* Clé publique 

Extrayez les clés publique et privée encodées en Base64 à partir du fichier PFX. Pour en savoir plus sur les propriétés, consultez [type de ressource keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Assurez-vous que le keyId de keycredential utilisé pour « signer » correspond au keyId de passwordCredential.

Vous pouvez générer le `customkeyIdentifier` en obtenant le code de hachage de l’empreinte du certificat.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Requête

> [!NOTE]
> La valeur « key » dans la propriété keyCredentials est raccourcie pour des raisons de lisibilité. La valeur est encodée en base 64. Pour la clé privée, la propriété `usage` est « Sign ». Pour la clé publique, la propriété `usage` est « Verify ».

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Activer la clé de signature personnalisée

Vous devez définir la propriété `preferredTokenSigningKeyThumbprint` sur l’empreinte du certificat que vous souhaitez qu’Azure AD utilise pour signer la réponse SAML. 

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Étape 5 : Affecter des utilisateurs

### <a name="assign-users-and-groups-to-the-application"></a>Affecter des utilisateurs et des groupes à l’application

Affecter l’utilisateur suivant au principal du service et affecter AWS_Role1. 

| Nom  | id  |
|---------|---------|
| ID d’utilisateur (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Type (principalType) | Utilisateur |
| ID de rôle d’application (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Requête

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Pour plus d’informations, consultez le type de ressource [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0).

## <a name="step-6-configure-the-application-side"></a>Étape 6 : Configurer du côté de l'application

### <a name="get-azure-ad-saml-metadata"></a>Obtenir des métadonnées SAML Azure AD

Utilisez l’URL suivante pour obtenir les métadonnées SAML Azure AD pour l’application configurée spécifique. Les métadonnées contiennent des informations telles que le certificat de signature, les éléments entityID Azure AD et SingleSignOnService Azure AD, entre autres.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Étapes suivantes
- [Utiliser des API Microsoft Graph pour configurer l’approvisionnement des utilisateurs](../app-provisioning/application-provisioning-configure-api.md)
- [Utiliser le rapport d’activité des applications AD FS pour migrer des applications vers Azure AD](migrate-adfs-application-activity.md)
