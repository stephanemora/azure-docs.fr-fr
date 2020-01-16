---
title: Comprendre le manifeste d’application Azure Active Directory | Microsoft Docs
description: Présentation détaillée de l’utilisation du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire Azure AD et permet de faciliter l’autorisation OAuth, le consentement et bien plus encore.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64823dbe2595d7896b9339745f6c8642a32f74e9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638695"
---
# <a name="azure-active-directory-app-manifest"></a>Manifeste d’application Azure Active Directory

Le manifeste d’application contient une définition de tous les attributs d’un objet d’application dans la plateforme d’identité Microsoft. Il sert également de mécanisme de mise à jour de l’objet d’application. Pour plus d’informations sur l’entité Application et son schéma, consultez la [documentation relative à l’entité Application de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Vous pouvez configurer les attributs d’une application via le portail Azure ou par programmation à l’aide de [l’API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) ou de [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Toutefois, il existe des scénarios dans lesquels vous devez modifier le manifeste de l’application pour configurer un attribut de l’application. Ces scénarios sont les suivants :

* Si vous avez inscrit l’application en tant que comptes Microsoft personnels et Azure AD multi-locataires, vous ne pouvez pas modifier les comptes Microsoft pris en charge dans l’interface utilisateur. À la place, vous devez utiliser l’éditeur de manifeste d’application pour changer le type de compte pris en charge.
* Si vous avez besoin de définir les autorisations et les rôles que votre application prend en charge, vous devez modifier le manifeste de l’application.

## <a name="configure-the-app-manifest"></a>Configurer le manifeste de l’application

Pour configurer le manifeste de l’application :

1. Accédez au [portail Azure](https://portal.azure.com). Recherchez et sélectionnez le service **Azure Active Directory**.
1. Sélectionnez **Inscriptions d’applications**.
1. Sélectionnez l’application à configurer.
1. sélectionner la section **Manifeste** sur la **page de présentation** de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de modifier le manifeste depuis le portail. Si vous le souhaitez, vous pouvez sélectionner **Télécharger** pour modifier localement le manifeste, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application.

## <a name="manifest-reference"></a>Référence du manifeste

> [!NOTE]
> Si vous ne voyez pas la colonne **Exemple de valeur** après la **Description**, agrandissez la fenêtre de votre navigateur et faites défiler/balayez jusqu’à ce que vous voyiez la colonne **Exemple de valeur**.

| Clé  | Type de valeur | Description  | Valeur d'exemple |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 nullable | Spécifie la version de jeton d’accès acceptée par la ressource. Ce paramètre modifie la version et le format du JWT produit, indépendamment du point de terminaison ou du client utilisé pour demander le jeton d’accès.<br/><br/>Le point de terminaison utilisé, v1.0 ou v2.0, est choisi par le client et affecte uniquement la version d’id_tokens. Les ressources doivent explicitement configurer `accesstokenAcceptedVersion` pour indiquer le format de jeton d’accès pris en charge.<br/><br/>Les valeurs possibles pour `accesstokenAcceptedVersion` sont 1, 2 ou null. Si la valeur est Null, ce paramètre est défini par défaut sur 1, ce qui correspond au point de terminaison v1.0. <br/><br/>Si `signInAudience` est `AzureADandPersonalMicrosoftAccount`, la valeur doit être `2`  | `2` |
| `addIns` | Collection | Définit un comportement personnalisé qu’un service consommateur peut utiliser pour appeler une application dans des contextes spécifiques. Par exemple, les applications pouvant restituer des flux de fichiers peuvent définir la propriété addIns pour sa fonctionnalité « filehandler ». Ce paramètre permet aux services tels qu’Office 365 d’appeler l’application dans le contexte d’un document sur lequel l’utilisateur travaille. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | Spécifie le type d’application de secours. Azure AD déduit le type d’application de replyUrlsWithType par défaut. Dans certains scénarios, Azure AD ne peut pas déterminer le type de l’application cliente. C’est le cas, par exemple, pour un flux [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) dans lequel une requête HTTP est envoyée sans redirection d’URL. Dans ce cas, Azure AD interprète le type d’application en fonction de la valeur de cette propriété. Si cette valeur est définie sur true, le type d’application de secours est défini en tant que client public, une application installée s’exécutant sur un appareil mobile par exemple. La valeur par défaut est false, ce qui signifie que le type d’application de secours est client confidentiel, une application web par exemple. | `false` |
| `availableToOtherTenants` | Boolean | valeur True si l’application est partagée avec d’autres locataires ; Sinon, false. <br><br> _Remarque : Il est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `signInAudience` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | |
| `appId` | String | Spécifie l’identificateur unique de l’application qui est affectée à une application par Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Collection | Spécifie la collection de rôles qu’une application peut déclarer. Ces rôles peuvent être assignés aux utilisateurs, groupes ou principaux du service. Pour découvrir des exemples et des informations supplémentaires, consultez l’article [Ajouter des rôles d’application dans votre application et les recevoir dans le jeton](howto-add-app-roles-in-azure-ad-apps.md). | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | Nom d’affichage de l’application. <br><br> _Remarque : Il est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `name` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | `"MyRegisteredApp"` |
| `errorUrl` | String | Non prise en charge. | |
| `groupMembershipClaims` | String | Configure la revendication `groups` émise dans un utilisateur ou un jeton d’accès OAuth 2.0 attendu par l’application. Pour définir cet attribut, utilisez l’une des valeurs de chaîne valides suivantes :<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (pour groupes de sécurité et rôles Azure AD)<br/>- `"All"` (ceci permet d’obtenir tous les groupes de sécurité, les groupes de distribution et les rôles d’annuaire Azure AD dont l’utilisateur connecté est membre) | `"SecurityGroup"` |
| `homepage` | String | URL vers la page d’accueil de l’application. <br><br> _Remarque : Il est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `signInUrl` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | `"https://MyRegisteredApp"` |
| `objectId` | String | Identificateur unique de l’application dans le répertoire. <br><br> _Remarque : Il est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `id` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | Les revendications facultatives retournées dans le jeton par le service d’émission de jeton de sécurité pour cette application spécifique.<br>À ce stade, les applications qui prennent en charge des comptes personnels et Azure AD (inscrites par le biais du portail d’inscription des applications) ne peuvent pas utiliser de revendications facultatives. Cependant, les applications enregistrées uniquement pour Azure AD à l’aide du point de terminaison v2.0 peuvent obtenir les revendications facultatives requises dans le manifeste. Pour plus d’informations, consultez les [revendications facultatives](active-directory-optional-claims.md). | `null` |
| `id` | String | Identificateur unique de l’application dans le répertoire. Cet ID n’est l’identificateur utilisé pour identifier l’application d’une transaction de protocole. Il est utilisé pour référencer l’objet dans les requêtes de répertoire. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Tableau de chaînes | Les URI définis par l’utilisateur qui identifient de manière unique une application web au sein de son locataire Azure AD ou au sein d’un domaine personnalisé vérifié si l’application est multi-locataires. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | Spécifie les liens vers les conditions d’utilisation et la déclaration de confidentialité de l’application. Les conditions d’utilisation et la déclaration de confidentialité sont présentées aux utilisateurs par le biais de l’expérience de consentement de l’utilisateur. Pour plus d’informations, consultez [ Ajouter des conditions d’utilisation et une déclaration de confidentialité pour les applications Azure AD inscrites](howto-add-terms-of-service-privacy-statement.md) | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Collection | Comporte des références à des informations d’identification de l’application affectée, des secrets partagés basés sur chaîne et des certificats X.509. ). | <code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Tableau de chaînes | Utilisé pour le regroupement de consentement si vous avez une solution qui contient deux parties : une application cliente et une application d’API web personnalisée. Si vous entrez l’appID de l’application cliente dans cette valeur, l’utilisateur n’aura à donner son consentement à l’application cliente qu’une seule fois. Azure AD saura que consentir au client signifie implicitement consentir à l’API web. Il provisionnera automatiquement et simultanément les principaux de service du client et de l’API web. L’application cliente et de l’API web doivent être enregistrées dans le même locataire. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | Lisez uniquement la valeur qui pointe vers l’URL CDN du logo qui a été chargé dans le portail. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | URL pour se déconnecter de l’application. | `"https://MyRegisteredAppLogout"` |
| `name` | String | Nom d’affichage de l’application. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | Spécifie si cette application web peut demander des jetons d’accès de flux OAuth2.0 implicites. La valeur par défaut est false. Cet indicateur est utilisé pour les applications basées sur un navigateur, telles que des applications monopages Javascript. Pour plus d’informations, entrez `OAuth 2.0 implicit grant flow` dans la table des matières et consultez les rubriques relatives au flux implicite. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | Spécifie si cette application web peut demander des jetons d’ID de flux OAuth2.0 implicites. La valeur par défaut est false. Cet indicateur est utilisé pour les applications basées sur un navigateur, telles que des applications monopages Javascript. | `false` |
| `oauth2Permissions` | Collection | Spécifie la collection d’étendues d’autorisation OAuth 2.0 que l’application d’API web (ressource) expose aux applications clientes. Ces étendues d’autorisation peuvent être accordées aux applications clientes durant le consentement. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | Spécifie si, dans le cadre des requêtes de jeton OAuth 2.0, Azure AD autorise les requêtes POST, par opposition aux requêtes GET. La valeur par défaut est false, ce qui indique que seules les requêtes GET sont autorisées. | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` spécifie les pays dans lesquels l’application est bloquée pour les utilisateurs mineurs.<br>`legalAgeGroupRule` spécifie la règle de groupe de tranche d’âge légal qui s’applique aux utilisateurs de l’application. Peut être défini sur `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Collection | Consultez la description de la propriété `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Collection | Répertorie les applications et les autorisations demandées pour un consentement implicite. Exige qu’un administrateur donne son consentement à l’application. preAuthorizedApplications ne demande pas que l’utilisateur donne son consentement pour les autorisations demandées. Les autorisations répertoriées dans preAuthorizedApplications n’exigent pas le consentement de l’utilisateur. Toutefois, les autorisations demandées supplémentaires non répertoriées dans preAuthorizedApplications requièrent le consentement de l’utilisateur. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | Spécifie si cette application est un client public (comme une application installée s’exécutant sur un appareil mobile). <br><br> _Remarque : Cette propriété est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `allowPublicClient` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | |
| `publisherDomain` | String | Le domaine vérifié de l’éditeur pour l’application. Lecture seule. | https://www.contoso.com |
| `replyUrls` | Tableau de chaînes | Cette propriété à valeurs multiples contient la liste des valeurs de redirect_uri inscrites que Azure AD acceptera comme destinations lors du renvoi des jetons. <br><br> _Remarque : Cette propriété est disponible uniquement dans l’expérience Inscriptions d’applications (hérité). Remplacé par `replyUrlsWithType` dans l’expérience [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908)._ | |
| `replyUrlsWithType` | Collection | Cette propriété à valeurs multiples contient la liste des valeurs de redirect_uri inscrites que Azure AD acceptera comme destinations lors du renvoi des jetons. Chaque valeur d’URI doit contenir une valeur de type d’application associée. Les valeurs de type prises en charge sont les suivantes : <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> En savoir plus sur les restrictions et limitations de [replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url). | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Collection | Avec le consentement dynamique, `requiredResourceAccess` gère l’expérience de consentement administrateur et l’expérience de consentement de l’utilisateur pour les utilisateurs qui utilisent le consentement statique. Toutefois, ce paramètre ne gère pas l’expérience de consentement de l’utilisateur pour le cas général.<br>`resourceAppId` est l’identificateur unique de la ressource à laquelle l’application souhaite accéder. Cette valeur doit être égale à l’appID déclaré sur l’application de ressource cible.<br>`resourceAccess` est un tableau qui répertorie les étendues d’autorisations OAuth2.0 et les rôles d’application requis par l’application à partir de la ressource spécifiée. Contient les valeurs `id` et `type` des ressources spécifiées. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | URL vers les métadonnées SAML pour l’application. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | Spécifie l’URL vers la page d’accueil de l’application. | `https://MyRegisteredApp` |
| `signInAudience` | String | Spécifie les comptes Microsoft pris en charge pour l’application actuelle. Les valeurs prises en charge sont les suivantes :<ul><li>**AzureADMyOrg** : utilisateurs avec un compte professionnel ou scolaire Microsoft dans le locataire Azure AD de mon organisation (par exemple, un locataire unique)</li><li>**AzureADMultipleOrgs** : utilisateurs avec un compte professionnel ou scolaire Microsoft dans le locataire Azure AD de n’importe quelle organisation (par exemple, multilocataire)</li> <li>**AzureADandPersonalMicrosoftAccount** : utilisateurs avec un compte Microsoft personnel, ou un compte professionnel ou scolaire dans le locataire Azure AD de n’importe quelle organisation</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Tableau de chaînes | Chaînes personnalisées pouvant être utilisées pour classer et identifier l’application. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Problèmes courants

### <a name="manifest-limits"></a>Limites du manifeste

Un manifeste de l’application comporte plusieurs attributs désignés comme des collections ; par exemple, approles keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess et oauth2Permissions. Dans le manifeste d’application complet pour n’importe quelle application, le nombre total d’entrées dans toutes les collections combinées a été limité à 1 200. Si vous avez déjà spécifié 100 URI de redirection dans le manifeste d’application, il ne vous reste que 1 100 entrées restantes à utiliser dans toutes les autres collections combinées qui composent le manifeste.

> [!NOTE]
> Dans le cas où vous essayez d’ajouter plus de 1 200 entrées dans le manifeste d’application, vous pouvez rencontrer une erreur **« Échec de mise à jour d’application xxxxxx. Détails de l’erreur : La taille du manifeste a dépassé sa limite. Réduisez le nombre de valeurs et renouvelez votre demande. »**

### <a name="unsupported-attributes"></a>Attributs non pris en charge

Le manifeste d’application représente le schéma du modèle d’application sous-jacent dans Azure AD. À mesure que le schéma sous-jacent évolue, l’éditeur de manifeste sera mis à jour pour refléter le nouveau schéma de temps à autre. Par conséquent, vous pouvez remarquer de nouveaux attributs qui apparaissent dans le manifeste d’application. Dans de rares occasions, vous pouvez remarquer une modification syntaxique ou sémantique dans les attributs existants, ou que des attributs ne sont plus pris en charge. Par exemple, vous verrez les nouveaux attributs dans les [inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) qui sont nommés différemment dans l’expérience des inscriptions d’applications (hérité).


| Inscriptions d’applications (hérité)| Inscriptions des applications           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Pour obtenir une description de ces attributs, consultez la section [Référence du manifeste](#manifest-reference).

Lorsque vous essayez de charger un manifeste téléchargé précédemment, vous pouvez voir les erreurs suivantes. Cette erreur est probablement due au fait que l’éditeur de manifeste prend désormais en charge une version plus récente du schéma, qui ne correspond pas à celle que vous tentez de charger.

- « **Échec de mise à jour de l’application xxxxxx. Détail de l’erreur : Identificateur d’objet non valide 'undefined'. [].** »
- « **Échec de mise à jour de l’application xxxxxx. Détail de l’erreur : Une ou plusieurs valeurs de propriété spécifiées ne sont pas valides. [].** »
- « **Échec de mise à jour de l’application xxxxxx. Détail de l’erreur : Pas autorisé à définir availableToOtherTenants dans cette version d’api pour une mise à jour. [].** »
- « **Échec de mise à jour de l’application xxxxxx. Détail de l’erreur : Les mises à jour de la propriété « replyUrls » ne sont pas autorisées pour cette application. Utilisez plutôt la propriété 'replyUrlsWithType'. [].** »
- « **Échec de mise à jour de l’application xxxxxx. Détail de l’erreur : Une valeur sans nom de type a été trouvée et aucun type attendu n’est disponible. Lorsque le modèle est spécifié, chaque valeur dans la charge utile doit avoir un type qui peut être spécifié dans la charge utile, explicitement par l’appelant ou implicitement déduite de la valeur parente. []**  »

Lorsque vous voyez l’une de ces erreurs, nous recommandons l’une des actions suivantes :

1. Modifiez les attributs individuellement dans l’éditeur de manifeste au lieu de charger un manifeste téléchargé précédemment. Utilisez la table [référence du manifeste](#manifest-reference) pour comprendre la syntaxe et la sémantique des anciens et des nouveaux attributs afin que vous puissiez modifier correctement les attributs qui vous intéresse. 
1. Si votre flux de travail vous oblige à enregistrer les manifestes dans votre référentiel de code source pour une utilisation ultérieure, nous vous suggérons de relocaliser les manifestes enregistrés dans votre référentiel avec celui que vous voyez dans l’expérience **inscriptions d’applications**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la relation entre les objets du principal de service et les objets d’application dans une application, consultez la rubrique [Objets application et principal du service dans Azure AD](app-objects-and-service-principals.md).
* Consultez le [glossaire du développeur de plateforme d’identité Microsoft](developer-glossary.md) pour connaître les définitions de certaines des principales conceptions de développeur de plateforme d’identité Microsoft.

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
