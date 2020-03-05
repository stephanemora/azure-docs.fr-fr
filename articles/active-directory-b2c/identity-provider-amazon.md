---
title: Configurer l’inscription et la connexion avec un compte Amazon
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Amazon dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188458"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Création d’une application Amazon

Pour utiliser un compte Amazon en tant que [fournisseur d’identité](authorization-code-flow.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer dans votre locataire une application qui le représente. Si vous ne disposez pas encore d’un compte Amazon, vous pouvez en créer un à l’adresse [https://www.amazon.com/](https://www.amazon.com/).

1. Connectez-vous au [Centre de développement Amazon](https://login.amazon.com/) avec les informations d’identification de votre compte Amazon.
1. Si ce n’est déjà fait, cliquez sur **Sign Up**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
1. Cliquez sur **Enregistrer une nouvelle application**.
1. Entrez un **Nom**, une **Description** et une **URL d’avis de confidentialité**, puis cliquez sur **Enregistrer**. La déclaration de confidentialité est une page que vous gérez et qui fournit des informations de confidentialité aux utilisateurs.
1. Dans la section **Paramètres web**, copiez les valeurs **ID client**. Sélectionnez **Montrer le secret** pour obtenir la clé secrète client et copiez-la. Vous aurez besoin de ces deux valeurs pour configurer un compte Amazon en tant que fournisseur d’identité dans votre locataire. **Client Secret** est une information d’identification de sécurité importante.
1. Dans la section **Paramètres web**, sélectionnez **Modifier**, puis entrez `https://your-tenant-name.b2clogin.com` dans **Origines JavaScript autorisées** et `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de retour autorisées**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
1. Cliquez sur **Enregistrer**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurer un compte Amazon comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Amazon**.
1. Saisissez un **Nom**. Par exemple, *Amazon*.
1. Dans **ID client**, entrez l’ID client de l’application Amazon que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
