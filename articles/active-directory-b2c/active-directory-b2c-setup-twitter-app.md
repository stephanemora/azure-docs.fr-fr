---
title: Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Twitter dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bc13a578308344e9395b14313c8363ae3cfa1ff2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844379"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C

## <a name="create-an-application"></a>Création d'une application

Pour utiliser Twitter comme fournisseur d’identité dans Azure AD B2C, vous devez créer une application Twitter. Si vous ne disposez pas déjà d’un compte Twitter, vous pouvez en créer un à l’adresse [https://twitter.com/signup](https://twitter.com/signup).

1. Connectez-vous au site web [Développeurs Twitter](https://developer.twitter.com/en/apps) avec les informations d’identification de votre compte Twitter.
2. Sélectionnez **Créer une application**.
3. Entrez un **Nom de l’application** et une **Description de l’application**.
4. Dans **URL du site web**, entrez `https://your-tenant.b2clogin.com`. Remplacez `your-tenant` par le nom de votre locataire. Par exemple : https://contosob2c.b2clogin.com.
5. Dans le champ **Callback URL** (URL de rappel), entrez `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Remplacez `your-tenant` par le nom de votre locataire et `your-user-flow-Id` par l’identificateur de votre flux d’utilisateur. Par exemple : `b2c_1A_signup_signin_twitter`. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
6. Au bas de la page, lisez et acceptez les termes du contrat, puis sélectionnez **Créer**.
7. Dans la page **Détails de l’application**, sélectionnez **Modifier > Modifier les détails**, cochez la case **Activer la connexion avec Twitter**, puis sélectionnez **Enregistrer**.
8. Sélectionnez **Clés et jetons** et prenez note des valeurs **Clé API de consommateur** et **Clé secrète API de consommateur** à utiliser ultérieurement.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Donnez-lui un **Nom**. Par exemple, entrez *Twitter*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Twitter**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez la clé API comme **ID client** et la clé secrète API comme **Clé secrète client**.
8. Cliquez sur **OK**, puis sur **Create** (Créer) pour enregistrer votre configuration Twitter.