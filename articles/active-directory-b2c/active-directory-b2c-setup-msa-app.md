---
title: Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Microsoft dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e9c42429a8feb60ade27afe998cb9a267f1603dd
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180901"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft

Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Microsoft, vous pouvez en obtenir un à l’adresse [https://www.live.com/](https://www.live.com/).

1. Connectez-vous au [Portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) avec vos informations d'identification Microsoft.
2. En haut à droite, sélectionnez **Ajouter une application**.
3. Entrez un **nom** pour votre application. Par exemple, *MSAapp1*.
4. Sélectionnez **Générer un nouveau mot de passe** et veillez à copier le mot de passe à utiliser quand vous configurez le fournisseur d’identité. Copiez également l’**ID de l’application**. 
5. Sélectionnez **Ajouter une plateforme**, puis choisissez **Web**.
4. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de redirection**. Remplacez `your-tenant-name` par le nom de votre locataire.
5. Sélectionnez **Enregistrer**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configuration d’un compte Microsoft en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, saisissez *MSA*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Compte Microsoft**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’Id d’application enregistré précédemment en tant qu’**ID Client** et entrez le mot de passe enregistré en tant que **clé secrète client** de l’application de compte Microsoft créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte Microsoft.

