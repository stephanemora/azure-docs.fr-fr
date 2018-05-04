---
title: Configuration de Twitter pour Azure AD B2C | Microsoft Docs
description: Proposez l’inscription et la connexion à des clients disposant de comptes Twitter dans vos applications sécurisées par Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Proposer l’inscription et la connexion à des clients disposant de comptes Twitter et utilisant Azure AD B2C

## <a name="create-a-twitter-application"></a>Création d'une application Twitter
Pour utiliser Twitter en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Twitter et lui fournir les paramètres appropriés. Pour ce faire, vous devez disposer d’un compte développeur Twitter. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://twitter.com/signup](https://twitter.com/signup).

1. Accédez aux [Applications Twitter](https://apps.twitter.com/) et connectez-vous à l’aide de vos informations d’identification.
2. Cliquez sur **Create New App**. 
3. Dans le formulaire, indiquez une valeur pour **Name** (Nom), **Description** (Description) et **Website** (Site Web).
4. Dans le champ **Callback URL** (URL de rappel), entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Assurez-vous de remplacer **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com).
5. Cochez la case pour accepter le **contrat pour les développeurs** et cliquez sur **Create your Twitter application** (Créer votre application Twitter).
6. Une fois l’application créée, cliquez sur **Clés et jetons d’accès**.
7. Copiez les informations de **Consumer Key** (Clé du client) et de **Consumer Secret** (Secret du client). Vous aurez besoin de ces deux valeurs pour configurer Twitter en tant que fournisseur d’identité dans votre client.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire
1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre abonné Azure AD B2C. 
2. Pour basculer vers votre abonné Azure AD B2C, sélectionnez le répertoire Azure AD B2C dans le coin supérieur droit du portail.
3. Cliquez sur **Tous les services**, puis sélectionnez **Azure AD B2C** à partir de la liste des services sous **Sécurité + Identité**.
4. Cliquez sur **Fournisseurs d’identité**.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Twitter ».
5. Cliquez sur **Type de fournisseur d'identité**, sélectionnez **Twitter (préversion)**, puis cliquez sur **OK**.
6. Cliquez sur **Set up this identity provider** (Configurer ce fournisseur d’identité) et entrez la **clé du client** Twitter pour **l’ID du client** et le **secret du client** Twitter pour le **secret du client**.
7. Cliquez sur **OK**, puis sur **Create** (Créer) pour enregistrer votre configuration Twitter.

## <a name="next-steps"></a>Étapes suivantes

Créez ou modifiez une [stratégie intégrée](active-directory-b2c-reference-policies.md) et ajoutez Twitter comme fournisseur d’identité.