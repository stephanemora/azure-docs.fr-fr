---
title: Autorisations de rôle d’administrateur personnalisées pour la gestion de l’inscription des applications – Azure Active Directory | Microsoft Docs
description: Autorisations de rôle d’administrateur personnalisées pour la délégation de la gestion des identités.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f31c5928273973a9089ae9ef1fd184cdb78bbb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033382"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Les sous-types et les autorisations d’inscription de l’application dans Azure Active Directory

Cet article contient les autorisations d’inscription d’application actuellement disponibles pour les définitions de rôle personnalisées dans Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Autorisations monolocataire v. multi-locataires

Les autorisations de rôle personnalisé diffèrent pour les applications monolocataires et multi-locataires. Les applications monolocataires sont uniquement disponibles pour les utilisateurs de l’organisation Azure AD dans laquelle l’application est inscrite. Les applications multi-locataires sont disponibles pour toutes les organisations Azure AD. Les applications monolocataires sont définies comme ayant des **types de comptes pris en charge** définis sur « Comptes dans ce répertoire d’organisation uniquement ». Dans l’API Graph, les applications monolocataires ont la propriété signInAudience définie sur « AzureADMyOrg ».

## <a name="application-registration-subtypes-and-permissions"></a>Autorisations et sous-types d’inscription d’application

Consultez la [vue d’ensemble des rôles personnalisés](roles-custom-overview.md) pour obtenir une explication des termes généraux du sous-type, de l’autorisation et du jeu de propriétés. Les informations suivantes sont spécifiques aux inscriptions d’applications.

### <a name="subtypes"></a>Sous-types

Il n’existe qu’un seul sous-type d’inscription d’application – applications.myOrganization. Par exemple, microsoft.directory/applications.myOrganization/basic/update. Ce sous-type est défini sur la page d’**authentification** pour une inscription d’application spécifique, et correspond à la définition de la propriété signInAudience sur « AzureADMyOrg » à l’aide de l’API Graph ou de PowerShell. Le sous-type restreint l’autorisation aux inscriptions d’applications qui sont marquées comme accessibles uniquement par les comptes de votre organisation (applications monolocataires).

Vous pouvez utiliser l’autorisation restreinte pour accorder des autorisations de lecture ou de gestion aux applications internes uniquement sans accorder des autorisations de lecture ou de gestion aux applications accessibles par les comptes d’autres organisations.

Il existe des versions d’applications.myOrganization de toutes les autorisations de lecture et de mise à jour, ainsi que de l’autorisation de suppression. Il n’existe aucune version de création d’application.myOrganization pour l’instant. Les autorisations standard (par exemple, microsoft.directory/applications/basic/update) accordent des autorisations de lecture ou de gestion pour tous les types d’inscription d’application.

![Déclarer une application monolocataire ou une multi-locataire](./media/roles-custom-available-permissions/supported-account-types.png)

Les informations relatives aux autorisations suivantes pour la version préliminaire des rôles personnalisés sont répertoriées dans [Autorisations de rôle personnalisé disponibles dans Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Créer et supprimer

Deux autorisations sont disponibles pour accorder la possibilité de créer des inscriptions d’applications, chacune avec un comportement différent :

- **microsoft.directory/applications/createAsOwner** : En attribuant cette autorisation, le créateur est ajouté en tant que premier propriétaire de l'enregistrement de l'application créée. L'enregistrement de l'application créée est comptabilisé dans le quota de 250 objets créés du créateur.
- **microsoft.directory/applicationPolicies/create** : En attribuant cette autorisation, le créateur n’est pas ajouté en tant que premier propriétaire de l'enregistrement de l'application créée. L'enregistrement de l'application créée est comptabilisé dans le quota de 250 objets créés du créateur. Utilisez cette autorisation avec précaution, car rien n’empêche l’intervenant de créer des inscriptions d’applications tant que le quota au niveau du répertoire n’a pas été atteint. Si les deux autorisations sont affectées, l’autorisation est prioritaire.

Si les deux autorisations sont affectées, l’autorisation /create est prioritaire. Bien que l’autorisation /createAsOwner n’ajoute pas automatiquement le créateur comme premier propriétaire, les propriétaires peuvent être spécifiés lors de la création de l’inscription de l’application lors de l’utilisation des API Graph ou des cmdlets PowerShell.

Les autorisations de création autorisent l’accès à la commande **Nouvelle inscription**.

[Ces autorisations accordent l’accès à la commande Nouvelle inscription du portail](./media/roles-create-custom/new-custom-role.png)

Deux autorisations sont disponibles pour vous permettre de supprimer les inscriptions d’applications :

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Octroie la possibilité de supprimer des inscriptions d’applications indépendamment du sous-type ; autrement dit, des applications monolocataires et multi-locataires.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Donne la possibilité de supprimer des inscriptions d’applications limitées à celles qui sont accessibles uniquement aux comptes de votre organisation ou à des applications monolocataires (sous-type myOrganization).

![Ces autorisations accordent l’accès à la commande Supprimer l’inscription d’application](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Lors de l’affectation d’un rôle qui contient des autorisations de création, l’attribution de rôle doit être effectuée au niveau de l’étendue du répertoire. Une autorisation de création affectée à une étendue de ressource n’accorde pas la possibilité de créer des inscriptions d’applications.

### <a name="read"></a>Lire

Tous les utilisateurs membres de l’organisation peuvent lire les informations d’inscription de l’application par défaut. Toutefois, cela n’est pas possible pour les utilisateurs invités et les principaux du service d’application. Si vous envisagez d’affecter un rôle à un utilisateur ou une application invité, vous devez inclure les autorisations de lecture appropriées.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Capacité de lire toutes les propriétés des applications monolocataires et multi-locataires en dehors des propriétés sensibles comme les informations d’identification.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Octroie les mêmes autorisations que microsoft.directory/applications/allProperties/read, mais uniquement pour les applications monolocataires.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>microsoft.directory/applications/standard/read: Octroie l’accès à tous les champs de la page de personnalisation de l’inscription d’application

![Cette autorisation accorde l’accès à la page de personnalisation de l’inscription des applications](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Octroie les mêmes autorisations que microsoft.directory/applications/standard/read, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Accorde la possibilité de lire la propriété des propriétaires sur des applications monolocataires et multi-locataires Octroie l’accès à tous les champs de la page propriétaires de l’inscription de l’application :

![Ces autorisations accordent l’accès à la page propriétaires d’inscription des applications.](./media/roles-custom-available-permissions/app-registration-owners.png)

Octroie l’accès aux propriétés suivantes sur l’entité application :

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Mettre à jour

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Octroie les mêmes autorisations que microsoft.directory/applications/allProperties/update, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Octroie l’accès à tous les champs de la page authentification de l’inscription de l’application :

![Cette autorisation accorde l’accès à la page d’authentification de l’inscription de l’application](./media/roles-custom-available-permissions/supported-account-types.png)

Octroie l’accès aux propriétés suivantes sur la ressource d’application :

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Octroie les mêmes autorisations que microsoft.directory/applications/audience/update, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Possibilité de mettre à jour les propriétés URL de réponse, URL de déconnexion, flux implicite et domaine du serveur de publication sur des applications monolocataires et multi-locataires. Octroie l’accès à tous les champs de la page d’authentification de l’inscription de l’application, à l’exception des types de comptes pris en charge :

![Octroie l’accès à l’authentification d’inscription d’application, mais pas aux types de comptes pris en charge](./media/roles-custom-available-permissions/supported-account-types.png)

 Octroie l’accès aux propriétés suivantes sur la ressource d’application :

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- AddIns
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Octroie les mêmes autorisations que microsoft.directory/applications/authentication/update, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Possibilité de mettre à jour le nom, le logo, l’URL de la page d’accueil, l’URL des conditions de service et les propriétés de l’URL de la déclaration de confidentialité sur les applications monolocataires et multilocataires. Octroie l’accès à tous les champs de la page de personnalisation de l’inscription d’application :

![Cette autorisation accorde l’accès à la page de personnalisation de l’inscription des applications](./media/roles-custom-available-permissions/app-registration-branding.png)

Octroie l’accès aux propriétés suivantes sur la ressource d’application :

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Octroie les mêmes autorisations que microsoft.directory/applications/basic/update, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Possibilité de mettre à jour les propriétés des certificats et des secrets client sur des applications monolocataires et multi-locataires. Octroie l’accès à tous les champs de la page certificats et secrets d’inscription d’application :

![Cette autorisation accorde l’accès à la page certificats et secrets d’inscription d’application](./media/roles-custom-available-permissions/app-registration-secrets.png)

Octroie l’accès aux propriétés suivantes sur la ressource d’application :
- AsymmetricKey
- EncryptedSecretKey
- KeyDescription
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Octroie les mêmes autorisations que microsoft.directory/applications/credentials/update, mais uniquement pour les applications à un seul annuaire.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Possibilité de mettre à jour la propriété du propriétaire sur les applications monolocataires et multi-locataires. Octroie l’accès à tous les champs de la page propriétaires de l’inscription de l’application :

![Ces autorisations accordent l’accès à la page propriétaires d’inscription des applications.](./media/roles-custom-available-permissions/app-registration-owners.png)

Octroie l’accès aux propriétés suivantes sur la ressource d’application :
- Propriétaires

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Octroie les mêmes autorisations que microsoft.directory/applications/owners/update, mais uniquement pour les applications monolocataire.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Possibilité de mettre à jour les autorisations déléguées, les autorisations d’application, les applications clientes autorisées, les autorisations requises et d’accorder des propriétés de consentement sur des applications monolocataires et multi-locataires. N’accorde pas la possibilité d’effectuer un consentement. Octroie l’accès à tous les champs des autorisations de l’API d’inscription d’application et expose une page API :

![Cette autorisation accorde l’accès à la page Autorisations de l’API d’inscription d’application](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Cette autorisation accorde l’accès à la page Exposer une API de l’inscription d’application](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Octroie l’accès aux propriétés suivantes sur la ressource d’application :

- AppIdentifierUri
- Entitlement
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Octroie les mêmes autorisations que microsoft.directory/applications/permissions/update, mais uniquement pour les applications monolocataire.

## <a name="required-license-plan"></a>Plan de licence obligatoire

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- Créer des rôles personnalisés à l’aide du [portail Azure, d’Azure AD PowerShell et de l’API Graph](roles-create-custom.md)
- [Afficher les affectations d’un rôle personnalisé](roles-view-assignments.md)
