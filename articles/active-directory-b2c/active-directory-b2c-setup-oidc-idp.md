---
title: Configurer l’inscription et la connexion avec OpenID Connect à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Configurez l’inscription et la connexion avec OpenID Connect à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9f6b65a4253b9cc7c04f397dde7ecab9a64d5ae0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845977"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec OpenID Connect à l’aide d’Azure Active Directory B2C

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.


[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) est un protocole d’authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs. La plupart des fournisseurs d’identité qui utilisent ce protocole, tels que [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), sont pris en charge dans Azure AD B2C. Cet article explique comment ajouter un fournisseur d’identité OpenID Connect personnalisé dans vos flux d’utilisateur.


## <a name="add-the-identity-provider"></a>Ajouter le fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Sélectionnez **Fournisseurs d’identité**, puis cliquez sur **Ajouter**.
5. Pour **Type de fournisseur d’identité**, sélectionnez **OpenID Connect (Préversion)**.

## <a name="configure-the-identity-provider"></a>Configurer le fournisseur d’identité

Chaque fournisseur d’identité OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour effectuer la connexion. Cela inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Le document de métadonnées OpenID Connect est toujours situé dans un point de terminaison qui se termine par `.well-known\openid-configuration`. Pour le fournisseur d’identité OpenID Connect que vous cherchez à ajouter, entrez l’URL de métadonnées.

Pour permettre aux utilisateurs de se connecter, le fournisseur d’identité exige des développeurs qu’ils inscrivent une application dans leur service. Cette application a un ID (appelé **ID client**) et une **clé secrète client**. Copiez ces valeurs à partir du fournisseur d’identité et entrez-les dans les champs correspondants.

> [!NOTE]
> Le secret client est facultatif. Toutefois, vous devez entrer un secret client si vous souhaitez utiliser le [flux de code d’autorisation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), qui utilise le secret pour échanger le code pour le jeton.

L’étendue définit les informations et les autorisations que vous souhaitez collecter à partir de votre fournisseur d’identité personnalisé. Les demandes OpenID Connect doivent contenir la valeur d’étendue `openid` afin de recevoir le jeton d’ID du fournisseur d’identité. Sans jeton d’ID, les utilisateurs ne peuvent pas se connecter à Azure AD B2C à l’aide du fournisseur d’identité personnalisé. D’autres étendues peuvent être ajoutées, séparées par un espace. Reportez-vous à la documentation du fournisseur d’identité personnalisé pour afficher les autres étendues pouvant être disponibles.

Le type de réponse décrit le type d’informations renvoyé dans l’appel initial au `authorization_endpoint` du fournisseur d’identité personnalisé. Les types de réponse suivants peuvent être utilisés :

- `code`: conformément au [flux de code d’autorisation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), un code est retourné vers Azure AD B2C. Azure AD B2C appelle ensuite le `token_endpoint` pour échanger le code pour le jeton.
- `token`: un jeton d’accès est retourné à Azure AD B2C par le fournisseur d’identité personnalisé.
- `id_token`: un jeton d’ID est retourné à Azure AD B2C par le fournisseur d’identité personnalisé.

Le mode de réponse définit la méthode qui doit être utilisée pour renvoyer les données du fournisseur d’identité personnalisé vers Azure Active Directory B2C. Les modes de réponse suivants peuvent être utilisés :

- `form_post`: ce mode de réponse est recommandé pour une sécurité optimale. La réponse est transmise via la méthode HTTP `POST`, avec le code ou le jeton encodé dans le corps au format `application/x-www-form-urlencoded`.
- `query`: le code ou le jeton est retourné en tant que paramètre de requête.

L’indication du domaine peut être utilisée pour passer directement à la page de connexion du fournisseur d’identité spécifié. Cela évite à l’utilisateur de sélectionner une option dans la liste des fournisseurs d’identité disponibles. Pour autoriser ce type de comportement, entrez une valeur pour l’indication du domaine. Pour passer au fournisseur d’identité personnalisé, ajoutez le paramètre `domain_hint=<domain hint value>` à la fin de votre demande lors de l’appel d’Azure AD B2C pour la connexion.

Une fois que le fournisseur d’identité personnalisé a renvoyé un jeton d’ID à Azure AD B2C, Azure AD B2C doit être en mesure de mapper les revendications du jeton reçu aux revendications qu’Azure AD B2C reconnaît et utilise. Pour chacun des mappages suivants, reportez-vous à la documentation du fournisseur d’identité personnalisé pour comprendre les revendications qui sont renvoyées dans les jetons du fournisseur d’identité :

- `User ID`: entrez la revendication qui fournit l’identificateur unique pour l’utilisateur connecté.
- `Display Name`: entrez la revendication qui fournit le nom complet de l’utilisateur.
- `Given Name`: entrez la revendication qui fournit le prénom de l’utilisateur.
- `Surname`: entrez la revendication qui fournit le nom de l’utilisateur.
- `Email`: entrez la revendication qui fournit l’adresse e-mail de l’utilisateur.

