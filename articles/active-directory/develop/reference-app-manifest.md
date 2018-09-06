---
title: Comprendre le manifeste d’application Azure Active Directory | Microsoft Docs
description: Présentation détaillée de l’utilisation du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire Azure AD et permet de faciliter l’autorisation OAuth, le consentement et bien plus encore.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127018"
---
# <a name="azure-active-directory-app-manifest"></a>Manifeste d’application Azure Active Directory

Les applications qui s’intègrent à Azure Active Directory (Azure AD) doivent être enregistrées avec un locataire Azure AD. Vous pouvez configurer l’application dans le [portail Azure](https://portal.azure.com) en sélectionnant **Azure Active Directory**, en choisissant l’application que vous souhaitez configurer, puis en sélectionnant **Manifeste**.

## <a name="manifest-reference"></a>Référence du manifeste

> [!NOTE]
> Si vous ne voyez pas les descriptions, agrandissez la fenêtre du navigateur ou faites défiler/balayez l’écran pour afficher les descriptions.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Clé  | Type de valeur | Exemple de valeur | Description  |
|---------|---------|---------|---------|
| `appID` | Chaîne d’identificateur | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Spécifie l’identificateur unique de l’application qui est affectée à une application par Azure AD. |
| `appRoles` | Type de tableau | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Spécifie la collection de rôles qu’une application peut déclarer. Ces rôles peuvent être assignés aux utilisateurs, groupes ou principaux du service. |
| `availableToOtherTenants`| booléenne | `true` | Si cette valeur est définie sur true, l’application est disponible pour d’autres locataires. Si la valeur est définie sur false, l’application n’est disponible que pour l’abonné dans laquelle il est enregistré. Pour plus d’informations, consultez [Procédure : connecter un utilisateur Azure AD à l’aide du modèle d’application mutualisée](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | chaîne | `MyRegisteredApp` | Nom d’affichage de l’application. |
| `errorURL` | chaîne | `http://MyRegisteredAppError` | URL des erreurs rencontrées dans une application. |
| `groupMembershipClaims` | chaîne | `1` | Un masque de bits qui configure la revendication `groups` émise dans un utilisateur ou un jeton d’accès OAuth 2.0 attendu par l’application. Les valeurs du masque de bits sont les suivantes :<br>0 : Aucun<br>1 : groupes de sécurité et rôles Azure AD<br>2 : Réservé<br>4 : Réservé<br>Définir le masque de bits sur 7 permet d’obtenir tous les groupes de sécurité, les groupes de distribution et les rôles d’annuaire Azure AD dont l’utilisateur connecté est membre. |
| `optionalClaims` | chaîne | `null` | Les revendications facultatives retournées dans le jeton par le service d’émission de jeton de sécurité pour cette application spécifique. Pour plus d’informations, consultez les [revendications facultatives](active-directory-optional-claims.md). |
| `acceptMappedClaims` | booléenne | `true` | Si cette valeur est définie sur `true`, elle permet à l’application d’utiliser le mappage des revendications sans spécifier une clé de signature personnalisée. |
| `homepage` | chaîne | `http://MyRegisteredApp` | Spécifie l’URL vers la page d’accueil de l’application. |
| `informationalUrls` | chaîne | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Spécifie les liens vers les conditions d’utilisation et la déclaration de confidentialité de l’application. Les conditions d’utilisation et la déclaration de confidentialité sont présentées aux utilisateurs par le biais de l’expérience de consentement de l’utilisateur. Pour plus d’informations, consultez [Procédure : ajouter les conditions d’utilisation et la déclaration de confidentialité des applications Azure AD inscrites](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Tableau de chaînes | `http://MyRegistererdApp` | Les URI définis par l’utilisateur qui identifient de manière unique une application web au sein de son locataire Azure AD ou au sein d’un domaine personnalisé vérifié si l’application est multi-locataires. |
| `keyCredentials` | Type de tableau | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Comporte des références à des informations d’identification de l’application affectée, des secrets partagés basés sur chaîne et des certificats X.509. Ces informations d’identification sont utilisées lors de la requête de jetons d’accès (lorsque l’application agit en tant que client plutôt qu’en tant que ressource). |
| `knownClientApplications` | Type de tableau | `[GUID]` | Utilisé pour le regroupement de consentement si vous avez une solution qui contient deux parties : une application cliente et une application d’API web personnalisée. Si vous entrez l’appID de l’application cliente dans cette valeur, l’utilisateur n’aura à donner son consentement à l’application cliente qu’une seule fois. Azure AD saura que donner son consentement au client signifie implicitement donner son consentement à l’API web et fournira automatiquement des principaux du service au client et à l’API web en même temps. L’application cliente et de l’API web doivent être enregistrées dans le même locataire. |
| `logoutUrl` | chaîne | `http://MyRegisteredAppLogout` | URL pour se déconnecter de l’application. |
| `oauth2AllowImplicitFlow` | booléenne | `false` | Spécifie si cette application web peut exiger des jetons de flux OAuth2.0 implicites. La valeur par défaut est false. Cet indicateur est utilisé pour les applications basées sur un navigateur, telles que des applications d’une seule page Javascript. |
| `oauth2AllowUrlPathMatching` | booléenne | `false` | Spécifie si, dans le cadre de requêtes de jeton OAuth 2.0, Azure AD autorise un chemin d’accès correspondant pour l’URI de redirection par rapport aux replyUrls de l’application. La valeur par défaut est false. |
| `oauth2Permissions` | Type de tableau | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Spécifie la collection d’étendues d’autorisation OAuth 2.0 que l’application d’API web (ressource) expose aux applications clientes. Ces étendues d’autorisation peuvent être accordées aux applications clientes durant le consentement. |
| `oauth2RequiredPostResponse` | booléenne | `false` | Spécifie si, dans le cadre des requêtes de jeton OAuth 2.0, Azure AD autorise les requêtes POST, par opposition aux requêtes GET. La valeur par défaut est false, ce qui indique que seules les requêtes GET sont autorisées. |
| `objectId` | Chaîne d’identificateur | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Identificateur unique de l’application dans le répertoire. Cet ID n’est l’identificateur utilisé pour identifier l’application d’une transaction de protocole. Il est utilisé pour référencer l’objet dans les requêtes de répertoire. |
| `parentalControlSettings` | chaîne | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` spécifie les pays dans lesquels l’application est bloquée pour les utilisateurs mineurs.<br>`legalAgeGroupRule` spécifie la règle de groupe de tranche d’âge légal qui s’applique aux utilisateurs de l’application. Peut être défini sur `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  |
| `passwordCredentials` | Type de tableau | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Consultez la description de la propriété `keyCredentials`. |
| `publicClient` | booléenne | `false` | Spécifie si une application est un client public, comme une application installée s’exécutant sur un appareil mobile. La valeur par défaut est false. |
| `replyUrls` | Tableau de chaînes | `"http://localhost"` | Cette propriété à valeurs multiples contient la liste des valeurs de redirect_uri inscrites que Azure AD acceptera comme destinations lors du renvoi des jetons. |
| `requiredResourceAccess` | Type de tableau | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | Avec le consentement dynamique, `requiredResourceAccess` gère l’expérience de consentement administrateur et l’expérience de consentement de l’utilisateur pour les utilisateurs qui utilisent le consentement statique. Toutefois, ceci ne gère pas l’expérience de consentement de l’utilisateur pour le cas général.<br>`resourceAppId` est l’identificateur unique de la ressource à laquelle l’application souhaite accéder. Cette valeur doit être égale à l’appID déclaré sur l’application de ressource cible.<br>`resourceAccess` est un tableau qui répertorie les étendues d’autorisations OAuth2.0 et les rôles d’application requis par l’application à partir de la ressource spécifiée. Contient les valeurs `id` et `type` des ressources spécifiées. |
| `samlMetadataUrl` | chaîne | `http://MyRegisteredAppSAMLMetadata` | URL vers les métadonnées SAML pour l’application. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la relation entre les objets du principal de service et les objets d’application dans une application, consultez la rubrique [Objets application et principal du service dans Azure AD](app-objects-and-service-principals.md).
* Consultez le [glossaire du développeur Azure AD](developer-glossary.md) pour connaître les définitions de certains des principaux concepts de développement Azure Active Directory (AD).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

