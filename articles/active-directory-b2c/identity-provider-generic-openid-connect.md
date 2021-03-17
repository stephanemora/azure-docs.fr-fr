---
title: Configurer l’inscription et la connexion avec un compte OpenID Connect
titleSuffix: Azure AD B2C
description: Configurez l’inscription et la connexion avec un fournisseur d’identité OpenID Connect dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448334"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec OpenID Connect à l’aide d’Azure Active Directory B2C

[OpenID Connect](openid-connect.md) est un protocole d’authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour la connexion sécurisée des utilisateurs. La plupart des fournisseurs d’identité qui utilisent ce protocole sont pris en charge dans Azure AD B2C. Cet article explique comment ajouter un fournisseur d’identité OpenID Connect personnalisé dans vos flux d’utilisateur.

## <a name="add-the-identity-provider"></a>Ajouter le fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le filtre **Répertoire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Contoso*.

## <a name="configure-the-identity-provider"></a>Configurer le fournisseur d’identité

Chaque fournisseur d’identité OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour effectuer la connexion. Cela inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Le document de métadonnées OpenID Connect est toujours situé dans un point de terminaison qui se termine par `.well-known/openid-configuration`. Pour le fournisseur d’identité OpenID Connect que vous cherchez à ajouter, entrez l’URL de métadonnées.

## <a name="client-id-and-secret"></a>ID et secret client

Pour permettre aux utilisateurs de se connecter, le fournisseur d’identité exige des développeurs qu’ils inscrivent une application dans leur service. Cette application a un ID (appelé **ID client**) et une **clé secrète client**. Copiez ces valeurs à partir du fournisseur d’identité et entrez-les dans les champs correspondants.

> [!NOTE]
> Le secret client est facultatif. Toutefois, vous devez entrer un secret client si vous souhaitez utiliser le [flux de code d’autorisation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), qui utilise le secret pour échanger le code pour le jeton.

## <a name="scope"></a>Étendue

L’étendue définit les informations et les autorisations que vous souhaitez collecter à partir de votre fournisseur d’identité personnalisé. Les demandes OpenID Connect doivent contenir la valeur d’étendue `openid` afin de recevoir le jeton d’ID du fournisseur d’identité. Sans jeton d’ID, les utilisateurs ne peuvent pas se connecter à Azure AD B2C à l’aide du fournisseur d’identité personnalisé. D’autres étendues peuvent être ajoutées, séparées par un espace. Reportez-vous à la documentation du fournisseur d’identité personnalisé pour afficher les autres étendues pouvant être disponibles.

## <a name="response-type"></a>Type de réponse

Le type de réponse décrit le type d’informations renvoyé dans l’appel initial au `authorization_endpoint` du fournisseur d’identité personnalisé. Les types de réponse suivants peuvent être utilisés :

* `code` : conformément au [flux de code d’autorisation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), un code est renvoyé vers Azure AD B2C. Azure AD B2C appelle ensuite le `token_endpoint` pour échanger le code pour le jeton.
* `id_token` : un jeton d’ID est renvoyé à Azure AD B2C par le fournisseur d’identité personnalisé.

## <a name="response-mode"></a>Mode de réponse

Le mode de réponse définit la méthode qui doit être utilisée pour renvoyer les données du fournisseur d’identité personnalisé vers Azure Active Directory B2C. Les modes de réponse suivants peuvent être utilisés :

* Le mode de réponse `form_post` est recommandé pour une sécurité optimale. La réponse est transmise via la méthode HTTP `POST`, avec le code ou le jeton encodé dans le corps au format `application/x-www-form-urlencoded`.
* `query` : le code ou le jeton est retourné en tant que paramètre de requête.

## <a name="domain-hint"></a>Indication du domaine

L’indication du domaine peut être utilisée pour passer directement à la page de connexion du fournisseur d’identité spécifié. Cela évite à l’utilisateur de sélectionner une option dans la liste des fournisseurs d’identité disponibles. Pour autoriser ce type de comportement, entrez une valeur pour l’indication du domaine. Pour passer au fournisseur d’identité personnalisé, ajoutez le paramètre `domain_hint=<domain hint value>` à la fin de votre demande lors de l’appel d’Azure AD B2C pour la connexion.

## <a name="claims-mapping"></a>Mappage des revendications

Une fois que le fournisseur d’identité personnalisé a renvoyé un jeton d’ID à Azure AD B2C, Azure AD B2C doit être en mesure de mapper les revendications du jeton reçu aux revendications qu’Azure AD B2C reconnaît et utilise. Pour chacun des mappages suivants, reportez-vous à la documentation du fournisseur d’identité personnalisé pour comprendre les revendications qui sont renvoyées dans les jetons du fournisseur d’identité :

* **ID utilisateur** : entrez la revendication qui fournit l'*identificateur unique* de l'utilisateur connecté.
* **Nom d'affichage** : entrez la revendication qui fournit le *nom d'affichage* ou le *nom complet* de l'utilisateur.
* **Prénom** : entrez la revendication qui fournit le *prénom* de l'utilisateur.
* **Patronyme** : entrez la revendication qui fournit le *nom* de l'utilisateur.
* **Adresse e-mail** : entrez la revendication qui fournit l'*adresse e-mail* de l'utilisateur.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d'identité à un flux d'utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d'utilisateur auquel vous souhaitez ajouter le fournisseur d'identité. 
1. Sous **Fournisseurs d'identité sociale**, sélectionnez le fournisseur d'identité que vous avez ajouté. Par exemple, *Contoso*.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d'utilisateur**.
1. Sur la page d'inscription ou de connexion, sélectionnez le fournisseur d'identité auquel vous souhaitez vous connecter. Par exemple, *Contoso*.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.
