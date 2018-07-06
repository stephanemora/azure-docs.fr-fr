---
title: Ajout de fournisseurs d’identité OpenID Connect à des stratégies intégrées dans Azure Active Directory B2C | Microsoft Docs
description: Guide de présentation sur l’ajout de fournisseurs OpenID Connect à des stratégies intégrées dans Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444223"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C : Ajouter un fournisseur d’identité OpenID Connect personnalisé dans des stratégies intégrées

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) est un protocole d’authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs. La plupart des fournisseurs d’identité qui utilisent ce protocole, tels que [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), sont pris en charge dans Azure AD B2C. Cet article explique comment ajouter un fournisseur d’identité OpenID Connect personnalisé dans vos stratégies intégrées.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Configuration d’un fournisseur d’identité OpenID Connect personnalisé

Pour ajouter un fournisseur d’identité OpenID Connect personnalisé :

1. Suivez ces étapes pour [accéder aux paramètres Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) dans le portail Azure.
1. Cliquez sur **Fournisseurs d’identité**.
1. Cliquez sur **Ajouter**.
1. Pour **Type de fournisseur d’identité**, sélectionnez **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Configuration du fournisseur d’identité OpenID Connect

#### <a name="metadata-url"></a>URL de métadonnées

Comme spécifié, chaque fournisseur d’identité OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour effectuer la connexion. Cela inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Le document de métadonnées OpenID Connect est toujours situé dans un point de terminaison qui se termine par `.well-known\openid-configuration`.

Pour le fournisseur d’identité OpenID Connect que vous cherchez à ajouter, entrez l’URL de métadonnées.

#### <a name="client-id-and-secret"></a>ID client et clé secrète

Pour permettre aux utilisateurs de se connecter, le fournisseur d’identité demande aux développeurs d’inscrire une application dans leur service. Cette application a un ID (appelé **ID client**) et une **clé secrète cliente**. Copiez ces valeurs à partir du fournisseur d’identité et entrez-les dans les champs correspondants.

> [!NOTE]
> La clé secrète cliente est facultative. Toutefois, vous devez entrer une clé secrète cliente si vous souhaitez utiliser le [flux de code d’autorisation](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), qui utilise la clé secrète pour échanger le code pour le jeton.

#### <a name="scope"></a>Étendue

Les étendues définissent les informations et les autorisations que vous souhaitez collecter à partir de votre fournisseur d’identité personnalisé. Les demandes OpenID Connect doivent contenir la valeur d’étendue `openid` afin de recevoir le jeton d’ID du fournisseur d’identité. Sans jeton d’ID, les utilisateurs ne sont pas en mesure de se connecter à Azure AD B2C à l’aide du fournisseur d’identité personnalisé.

D’autres étendues peuvent être ajoutées (en les séparant par un espace). Reportez-vous à la documentation du fournisseur d’identité personnalisé pour afficher les autres étendues pouvant être disponibles.

#### <a name="response-type"></a>Type de réponse

Le type de réponse décrit le type d’informations renvoyé dans l’appel initial au `authorization_endpoint` du fournisseur d’identité personnalisé. 

* `code` : conformément au [flux de code d’autorisation](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), un code est renvoyé vers Azure AD B2C. Azure AD B2C appelle ensuite le `token_endpoint` pour échanger le code pour le jeton.
* `token` : un jeton d’accès est renvoyé à Azure AD B2C par le fournisseur d’identité personnalisé.
* `id_token` : un jeton d’ID est renvoyé à Azure AD B2C par le fournisseur d’identité personnalisé.


#### <a name="response-mode"></a>Mode de réponse

Le mode de réponse définit la méthode qui doit être utilisée pour renvoyer les données du fournisseur d’identité personnalisé vers Azure Active Directory B2C.

* Le mode de réponse `form_post` est recommandé pour une sécurité optimale. La réponse est transmise via la méthode HTTP `POST`, avec le code ou le jeton encodé dans le corps au format `application/x-www-form-urlencoded`.
* `query` : le code ou le jeton est retourné en tant que paramètre de requête.


#### <a name="domain-hint"></a>Indication du domaine

L’indication du domaine peut être utilisée pour passer directement à la page de connexion du fournisseur d’identité spécifié. Cela évite à l’utilisateur de sélectionner une option dans la liste des fournisseurs d’identité disponibles. Pour autoriser ce type de comportement, entrez une valeur pour l’indication du domaine.

Pour passer au fournisseur d’identité personnalisé, ajoutez le paramètre `domain_hint=<domain hint value>` à la fin de votre demande lors de l’appel d’Azure AD B2C pour la connexion.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mappage des revendications à partir du fournisseur d’identité OpenID Connect

Une fois que le fournisseur d’identité personnalisé a renvoyé un jeton d’ID à Azure AD B2C, Azure AD B2C doit être en mesure de mapper les revendications du jeton reçu aux revendications qu’Azure AD B2C reconnaît et utilise. 

Pour chacun des mappages ci-dessous, reportez-vous à la documentation du fournisseur d’identité personnalisé pour comprendre les revendications qui sont renvoyées dans les jetons du fournisseur d’identité.

* `User ID` : entrez la revendication qui fournit l’identificateur unique pour l’utilisateur connecté.
* `Display Name` : entrez la revendication qui fournit le nom complet de l’utilisateur.
* `Given Name` : entrez la revendication qui fournit le prénom de l’utilisateur.
* `Surname` : entrez la revendication qui fournit le nom de l’utilisateur.
* `Email` : entrez la revendication qui fournit l’adresse e-mail de l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Ajoutez le fournisseur d’identité OpenID Connect personnalisé à votre [stratégie intégrée](active-directory-b2c-reference-policies.md).
