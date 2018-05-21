---
title: Autorisations dans Azure AD | Microsoft Docs
description: Découvrez-en plus sur les étendues et les autorisations dans Azure Active Directory et la manière de les utiliser.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="permissions-in-azure-ad"></a>Autorisations dans Azure AD
Azure Active Directory (Azure AD) utilise très souvent des autorisations pour les flux OAuth et OpenID Connect (OIDC). Quand votre application reçoit un jeton d’accès provenant d’Azure AD, ce dernier inclut des revendications qui décrivent les autorisations (également appelées étendues) que votre application possède par rapport à une ressource particulière. Le processus d’autorisation est ainsi facilité pour la ressource, car il a uniquement besoin de vérifier que votre jeton contient l’autorisation appropriée à l’API que vous appelez. 

## <a name="types-of-permissions"></a>Types d’autorisations
Azure AD définit deux types d’autorisations : 
* **Autorisations déléguées** : utilisées par les applications qui ont un utilisateur connecté présent. Pour ces applications, l’utilisateur ou un administrateur accorde les autorisations que l’application demande. Ensuite, l’application se voit déléguer une autorisation d’agir en tant qu’utilisateur connecté lors des appels à une API. En fonction de l’API, l’utilisateur peut ne pas être en mesure de donner son consentement à l’API directement et [avoir besoin d’un « consentement de l’administrateur ».](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)
* **Autorisations d’application** : utilisées par les applications qui s’exécutent sans utilisateur connecté présent ; par exemple, les applications qui s’exécutent en tant que services ou démons en arrière-plan. Les autorisations d’application peuvent uniquement être [consenties par un administrateur](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant), car elles sont généralement très puissantes et permettent d’accéder à des données situées au-delà des limites de l’utilisateur ou normalement réservées aux seuls administrateurs. 

Les autorisations effectives sont celles qu’a votre application lors des requêtes faites à une API. 

* Pour les autorisations déléguées, les autorisations effectives de votre application correspondent au niveau de privilège le moins élevé entre les autorisations déléguées que l’application a reçues (par le biais d’un consentement) et les privilèges de l’utilisateur actuellement connecté. Votre application ne peut jamais avoir plus de privilèges que l’utilisateur connecté. Au sein des organisations, les privilèges de l’utilisateur connecté peuvent être déterminés par la stratégie ou l’appartenance à un ou plusieurs rôles d’administrateur. Pour plus d’informations sur les rôles d’administrateur, consultez [Attribution de rôles d’administrateur dans Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Par exemple, supposons que votre application ait reçu l’autorisation déléguée `User.ReadWrite.All` dans Microsoft Graph. Cette autorisation permet nominalement à votre application de lire et mettre à jour le profil de chaque utilisateur dans une organisation. Si l’utilisateur connecté est un administrateur général, votre application est en mesure de mettre à jour le profil de chaque utilisateur de l’organisation. Toutefois, si l’utilisateur connecté n’a pas de rôle d’administrateur, votre application peut uniquement mettre à jour le profil de l’utilisateur connecté. Elle ne peut pas mettre à jour les profils des autres utilisateurs de l’organisation, car l’utilisateur pour lequel elle est autorisée à agir n’a pas ces privilèges.
* Pour les autorisations d’application, les autorisations effectives de votre application correspondent au niveau complet des privilèges impliqués par l’autorisation. Par exemple, une application qui a l’autorisation `User.ReadWrite.All` peut mettre à jour le profil de chaque utilisateur de l’organisation. 

## <a name="permission-attributes"></a>Attributs d’autorisation
Les autorisations dans Azure AD ont plusieurs propriétés qui aident les utilisateurs, administrateurs ou développeurs d’applications à prendre des décisions avisées sur les éléments auxquels l’autorisation accorde l’accès. 

> [!NOTE]
> Vous pouvez voir les autorisations exposées par une application Azure AD ou un principal du service à l’aide du portail Azure ou de PowerShell. Essayez ce script pour voir les autorisations exposées par Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nom de la propriété | Description | Exemples | 
| --- | --- | --- |
| ID | Valeur GUID qui identifie cette autorisation de façon unique. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Indique si cette étendue est disponible à l’utilisation. | true | 
| type | Indique si cette autorisation nécessite le consentement de l’utilisateur ou de l’administrateur. | Utilisateur | 
| AdminConsentDescription | Description présentée aux administrateurs pendant leur expérience de consentement. | Autorise l’application à lire des e-mails dans des boîtes aux lettres utilisateur. | 
| AdminConsentDisplayName | Nom convivial présenté aux administrateurs lors de leur expérience de consentement. | Accéder en lecture aux e-mails utilisateur | 
| UserConsentDescription | Description présentée aux utilisateurs pendant une expérience de consentement. |  Autorise l’application à lire des e-mails dans votre boîte aux lettres. | 
| UserConsentDisplayName | Nom convivial présenté aux utilisateurs pendant une expérience de consentement. | Lire vos e-mails | 
| Valeur | Chaîne utilisée pour identifier l’autorisation pendant des flux d’autorisation OAuth 2.0. Elle peut également être combinée avec la chaîne d’URI d’ID d’application pour former un nom d’autorisation complet. | `Mail.Read` | 

## <a name="types-of-consent"></a>Types de consentement
Les applications dans Azure AD reposent sur un consentement pour obtenir l’accès aux ressources ou API nécessaires. Il existe plusieurs types de consentement dont votre application peut avoir besoin pour fonctionner correctement. Si vous définissez des autorisations, vous devez également comprendre la façon dont vos utilisateurs vont accéder à votre application ou API.

* **Consentement de l’utilisateur statique** : se produit automatiquement pendant le [flux d’autorisation OAuth 2.0](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) quand vous spécifiez la ressource avec laquelle votre application veut interagir. Dans le cadre d’un consentement de l’utilisateur statique, votre application doit déjà avoir spécifié toutes les autorisations dont elle a besoin dans sa configuration dans le portail Azure. Si l’utilisateur (ou administrateur, selon le cas) n’a pas donné son consentement à cette application, Azure AD invite l’utilisateur à le donner à ce stade. 

    Découvrez-en plus sur l’inscription d’une application Azure AD qui demande l’accès à un ensemble statique d’API.
* **Consentement de l’utilisateur dynamique** : fonctionnalité du modèle d’application Azure AD v2. Dans ce scénario, votre application demande un ensemble d’étendues dont elle a besoin dans le [flux d’autorisation OAuth 2.0 pour des applications v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Si l’utilisateur n’a pas déjà donné son consentement, il est invité à le faire à ce stade. [Découvrez-en plus sur le consentement dynamique](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > Le consentement dynamique peut s’avérer pratique, mais il représente un véritable défi pour les autorisations qui nécessitent un consentement de l’administrateur, dans la mesure où l’expérience de consentement de l’administrateur n’a pas connaissance de ces autorisations au moment du consentement. Si vous avez besoin d’étendues à privilège administratif, votre application doit les inscrire dans le portail Azure.
  
* **Consentement de l’administrateur** : nécessaire quand votre application a besoin d’accéder à certaines autorisations à privilèges élevés. Les administrateurs sont ainsi certains de disposer de contrôles supplémentaires avant d’autoriser des applications ou des utilisateurs à accéder aux données à privilèges élevés de l’organisation. [Découvrez-en plus sur la manière d’accorder un consentement de l’administrateur](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Meilleures pratiques

### <a name="resource-best-practices"></a>Bonnes pratiques pour les ressources
Les ressources qui exposent des API doivent définir des autorisations très spécifiques des données ou actions qu’elles protègent. Celles-ci permettent de veiller à ce que les clients n’obtiennent pas une autorisation d’accéder à des données dont ils n’ont pas besoin et à ce que les utilisateurs soient bien informés sur les données auxquelles ils donnent leur consentement.

Les ressources doivent explicitement définir des autorisations `Read` et `ReadWrite` séparément. 

Les ressources doivent marquer toutes les autorisations qui permettent d’accéder à des données situées au-delà des limites de l’utilisateur comme des autorisations `Admin`. 

Les ressources doivent suivre le modèle d’affectation de noms suivant `Subject.Permission[.Modifier]` où `Subject` correspond au type de données disponible, `Permission` correspond à l’action qu’un utilisateur peut effectuer sur ces données et `Modifier` est éventuellement utilisé pour décrire les spécialisations d’une autre autorisation. Par exemple :  
* Mail.Read : autorise les utilisateurs à lire des messages. 
* Mail.ReadWrite : autorise les utilisateurs à lire ou écrire des messages.
* Mail.ReadWrite.All : autorise un administrateur ou utilisateur à accéder à tous les messages de l’organisation.

### <a name="client-best-practices"></a>Meilleures pratiques clientes
Demandez une autorisation uniquement pour les étendues dont votre application a besoin. Les applications qui ont trop d’autorisations courent le risque d’exposer des données utilisateur si elles sont compromises.

Les clients ne doivent pas demander des autorisations d’application et des autorisations déléguées à partir de la même application. En effet, une élévation de privilèges peut alors se produire et autoriser un utilisateur à accéder à des données auxquelles ses propres autorisations ne lui permettraient pas d’accéder. 




