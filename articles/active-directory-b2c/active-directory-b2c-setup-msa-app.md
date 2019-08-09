---
title: Configurer l’inscription et la connexion avec un compte Microsoft – Azure Active Directory B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes Microsoft dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055119"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft

Pour utiliser un compte Microsoft en tant que [fournisseur d'identité](active-directory-b2c-reference-oidc.md) dans Azure Active Directory (Azure AD) B2C, vous devez créer dans votre locataire une application qui le représente. Si vous n’avez pas encore de compte Microsoft, vous pouvez en obtenir un à l’adresse [https://www.live.com/](https://www.live.com/).

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Sélectionnez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple, *MSAapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** . Cette option cible l’ensemble le plus large d’identités Microsoft.

   Pour plus d’informations sur les différents types de compte disponibles, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Sous **URI de redirection (facultatif)** , sélectionnez **Web**, puis entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans la zone de texte. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C.
1. Sélectionnez **Inscrire**.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page Vue d’ensemble de l’application. Vous en aurez besoin au moment de configurer le fournisseur d’identité dans la section suivante.
1. Sélectionnez **Certificats et secrets**.
1. Cliquez sur **Nouveau secret client**.
1. Entrez une **Description** pour le secret, par exemple *Mot de passe 1 de l’application*, puis cliquez sur **Ajouter**.
1. Enregistrez le mot de passe de l’application affiché dans la colonne **VALEUR**. Vous en aurez besoin au moment de configurer le fournisseur d’identité dans la section suivante.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configuration d’un compte Microsoft en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
1. Entrez un **nom**. Par exemple, saisissez *MSA*.
1. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Compte Microsoft**, puis cliquez sur **OK**.
1. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID d’application (client) enregistré précédemment dans la zone de texte **ID client**, puis entrez le secret client que vous avez enregistré dans la zone de texte **Secret client**.
1. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte Microsoft.
