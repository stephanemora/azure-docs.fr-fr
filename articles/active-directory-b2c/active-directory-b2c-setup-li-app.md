---
title: Configurer l’inscription et la connexion avec un compte LinkedIn à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes LinkedIn dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1719da96a849bb5390745ec3df3ed11374bb8700
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180493"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte LinkedIn à l’aide d’Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Création d’une application LinkedIn

Pour utiliser un compte LinkedIn en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte LinkedIn, vous pouvez en obtenir un à l’adresse [https://www.linkedin.com/](https://www.linkedin.com/).

1. Connectez-vous au [site web Développeurs LinkedIn](https://www.developer.linkedin.com/) à l’aide des informations d’identification de votre compte LinkedIn.
2. Sélectionnez **Mes applications**, puis cliquez sur **Créer une application**.
3. Entrez les informations suivantes : **Nom de l’entreprise**, **Nom de l’application**, **Description de l’application**, **Logo de l’application**, **Utilisation de l’application**, **URL du site web**, **E-mail professionnel** et **Téléphone professionnel**.
4. Acceptez les **conditions d’utilisation de l’API LinkedIn**, puis cliquez sur **Envoyer**.
5. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous les trouverez sous **Clés d’authentification**. Vous aurez besoin de ces deux valeurs pour configurer LinkedIn en tant que fournisseur d’identité dans votre client. **Client Secret** est une information d’identification de sécurité importante.
6. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de redirection autorisées**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C. Sélectionnez **Ajouter**, puis cliquez sur **Mettre à jour**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configuration d’un compte LinkedIn en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Fournissez un **nom**. Par exemple, entrez *LinkedIn*.
6. Sélectionnez **Type de fournisseur d’identité**, **LinkedIn**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’ID client enregistré précédemment en tant **qu’ID Client** et entrez le secret client enregistré en tant que **Secret client** de l’application de compte LinkedIn créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte LinkedIn.

