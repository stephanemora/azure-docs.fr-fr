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
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3149d45f50c53209e3be6be6688c9c2ce8fb0555
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900348"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Créer une application dans la console de développement Amazon

Pour utiliser un compte Amazon en tant que fournisseur d’identité fédérée dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans vos [technologies et services de développement Amazon](https://developer.amazon.com). Si vous ne disposez pas encore d’un compte Amazon, vous pouvez en créer un à l’adresse [https://www.amazon.com/](https://www.amazon.com/).

> [!NOTE]  
> Utilisez les URL suivantes à l’**étape 8** ci-dessous, en remplaçant `your-tenant-name` par le nom de votre locataire. Quand vous entrez le nom de votre locataire, utilisez uniquement des minuscules même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
> - Dans **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. 
> - Dans **URL de retour autorisées**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`.

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurer un compte Amazon comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Amazon**.
1. Saisissez un **Nom**. Par exemple, *Amazon*.
1. Dans **ID client**, entrez l’ID client de l’application Amazon que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
