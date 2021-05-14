---
title: Personnaliser les revendications d’application de locataire Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Cette page décrit le mappage de revendications Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598888"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Procédure : Personnaliser des revendications émises dans des jetons pour une application spécifique dans un locataire (préversion)

> [!NOTE]
> Cette fonctionnalité remplace la [personnalisation des revendications](active-directory-saml-claims-customization.md) offerte au moyen du portail aujourd'hui. Sur la même application, si vous personnalisez des revendications à l’aide du portail en plus de la méthode Graph/PowerShell détaillée dans ce document, les jetons émis pour cette application ignorent la configuration définie dans le portail. Les configurations effectuées au moyen des méthodes décrites dans ce document n’apparaissent pas dans le portail.

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique. Soyez prêt à rétablir ou à supprimer les modifications. La fonctionnalité est disponible dans tout abonnement Azure Active Directory (Azure AD) durant la période de préversion publique. Toutefois, quand la fonctionnalité sera généralement disponible, il se peut que certains de ses aspects nécessitent un abonnement Azure AD Premium. Cette fonctionnalité prend en charge la configuration des stratégies de mappage de revendications pour les protocoles WS-Fed, SAML, OAuth et OpenID Connect.

Les administrateurs de locataire utilisent cette fonctionnalité pour personnaliser les revendications émises dans des jetons pour une application spécifique dans leur locataire. Vous pouvez utiliser des stratégies de mappage de revendications pour effectuer les opérations suivantes :

- sélectionner les revendications incluses dans les jetons ;
- créer des types de revendications inexistants ;
- choisir ou modifier la source des données émises dans des revendications spécifiques.

Dans cet article, nous décrivons quelques scénarios courants qui peuvent vous aider à comprendre comment utiliser le [type de stratégie de mappage de revendications](reference-claims-mapping-policy-type.md).

Quand vous créez une stratégie de mappage de revendications, vous pouvez également émettre une revendication à partir d’un attribut d’extension de schéma d’annuaire dans des jetons. Utilisez *ExtensionID* pour l’attribut d’extension au lieu de *ID* dans l’élément `ClaimsSchema`.  Pour plus d’informations sur les attributs d’extension, consultez [Utiliser des attributs de l’extension de schéma d’annuaire](active-directory-schema-extensions.md).

## <a name="prerequisites"></a>Prérequis

Dans les exemples suivants, vous créez, mettez à jour, liez et supprimez des stratégies pour les principaux du service. Des stratégies de mappage de revendications peuvent être attribuées uniquement à des objets de principal du service. Si vous débutez avec Azure AD, nous vous recommandons de vous [documenter sur l’obtention d’un locataire Azure Active Directory](quickstart-create-new-tenant.md) avant de continuer avec ces exemples.

Pour commencer, suivez les étapes ci-dessous :

1. Téléchargez la dernière [préversion publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Exécutez la commande Connect pour vous connecter à votre compte d'administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la commande suivante. Nous vous conseillons d’exécuter cette commande après la plupart des opérations dans les scénarios suivants afin de vérifier que vos stratégies sont créées comme prévu.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Omettre les revendications de base dans les jetons

Dans cet exemple, vous créez une stratégie qui supprime l’[ensemble de revendications de base](reference-claims-mapping-policy-type.md#claim-sets) dans les jetons qui sont envoyés aux principaux de service liés.

1. Créez une stratégie de mappage de revendications. Cette stratégie, liée à des principaux du service spécifiques, supprime l’ensemble de revendications de base des jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Inclure EmployeeID et TenantCountry comme des revendications dans les jetons

Dans cet exemple, vous créez une stratégie qui ajoute EmployeeID et TenantCountry à des jetons émis pour des principaux du service liés. EmployeeID est émis en tant que type de revendication de nom dans les jetons SAML et JWT. TenantCountry est émis en tant que type de revendication de pays/région dans les jetons SAML et JWT. Dans cet exemple, nous continuons à inclure les ensembles de revendications de base dans les jetons.

1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Utiliser une transformation de revendications dans les jetons

Dans cet exemple, vous créez une stratégie qui émet une revendication personnalisée « JoinedData » pour des jetons JWT émis pour des principaux du service liés. Cette revendication contient une valeur créée en joignant les données stockées dans l’attribut extensionattribute1 sur l’objet utilisateur avec « .sandbox ». Dans cet exemple, nous excluons l’ensemble de revendications de base des jetons.

1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Les applications qui reçoivent des jetons s’appuient sur le fait que les valeurs de revendication sont émises par Azure AD et ne peuvent pas être falsifiées. Toutefois, lorsque vous modifiez le contenu d’un jeton via des stratégies de mappage de revendications, ces hypothèses peuvent ne plus être correctes. Les applications doivent explicitement confirmer que les jetons ont été modifiés par le créateur de la stratégie de mappage de revendications pour se protéger contre les stratégies de mappage de revendications créées par des intervenants malveillants. Cela peut être fait des manières suivantes :

- Configurer une clé de signature personnalisée
- Mettrez à jour le manifeste d’application pour accepter les revendications mappées.
 
Sans cela, Azure AD renverra un code d’erreur [`AADSTS50146`](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Clé de signature personnalisée

Pour ajouter une clé de signature personnalisée à l’objet du principal de service, vous pouvez utiliser la cmdlet Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) pour créer des informations d’identification de clé de certificat pour votre objet d’application.

Les applications pour lesquelles le mappage de revendications est activé doivent valider leurs clés de signature de jeton en ajoutant `appid={client_id}` à leurs [demandes de métadonnées OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Le format du document de métadonnées OpenID Connect que vous utilisez se trouve ci-dessous :

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Mettre à jour le manifeste de l’application

Vous pouvez également définir la propriété `acceptMappedClaims` à la valeur `true` dans le [manifeste de l’application](reference-app-manifest.md). Comme indiqué dans le [type de ressource apiApplication](/graph/api/resources/apiapplication#properties), cela permet à une application d’utiliser le mappage de revendications sans spécifier de clé de signature personnalisée.

Cela nécessite que l’audience du jeton demandé utilise un nom de domaine vérifié de votre locataire Azure AD, ce qui signifie que vous devez vous assurer de définir `Application ID URI` (représenté par `identifierUris` dans le manifeste de l’application) par exemple sur `https://contoso.com/my-api` ou (simplement en utilisant le nom du locataire par défaut) `https://contoso.onmicrosoft.com/my-api`.

Si vous n’utilisez pas un domaine vérifié, Azure AD retourne un code d’erreur `AADSTS501461` avec le message *« AcceptMappedClaims est pris en charge uniquement pour une audience de jetons correspondant au GUID de l’application ou une audience dans les domaines vérifiés du locataire. Vous pouvez modifier l’identificateur de ressource ou utiliser une clé de signature propre à l’application. »*

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez l’article de référence sur le [type de stratégie de mappage des revendications](reference-claims-mapping-policy-type.md) .
- Pour savoir comment personnaliser les revendications émises dans le jeton SAML via le portail Azure, consultez [How to: Customize claims issued in the SAML token for enterprise applications](active-directory-saml-claims-customization.md) (Comment : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise)
- Pour plus d’informations sur les attributs d’extension, consultez [Utiliser des attributs de l’extension de schéma d’annuaire dans les revendications](active-directory-schema-extensions.md).
