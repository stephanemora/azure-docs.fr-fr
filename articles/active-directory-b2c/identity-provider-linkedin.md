---
title: Configurer l’inscription et la connexion avec un compte LinkedIn
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes LinkedIn dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188098"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte LinkedIn à l’aide d’Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Création d’une application LinkedIn

Pour utiliser un compte LinkedIn en tant que [fournisseur d’identité](authorization-code-flow.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer dans votre locataire une application qui le représente. Si vous n’avez pas déjà de compte LinkedIn, vous pouvez vous inscrire à l’adresse [https://www.linkedin.com/](https://www.linkedin.com/).

1. Connectez-vous au [site web Développeurs LinkedIn](https://www.developer.linkedin.com/) à l’aide des informations d’identification de votre compte LinkedIn.
1. Sélectionnez **Mes applications**, puis cliquez sur **Créer une application**.
1. Entrez les informations suivantes : **Nom de l’entreprise**, **Nom de l’application**, **Description de l’application**, **Logo de l’application**, **Utilisation de l’application**, **URL du site web**, **E-mail professionnel** et **Téléphone professionnel**.
1. Acceptez les **conditions d’utilisation de l’API LinkedIn**, puis cliquez sur **Envoyer**.
1. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous les trouverez sous **Clés d’authentification**. Vous aurez besoin de ces deux valeurs pour configurer LinkedIn en tant que fournisseur d’identité dans votre client. **Client Secret** est une information d’identification de sécurité importante.
1. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de redirection autorisées**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C. Sélectionnez **Ajouter**, puis cliquez sur **Mettre à jour**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configuration d’un compte LinkedIn en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **LinkedIn**.
1. Saisissez un **Nom**. Par exemple, *LinkedIn*.
1. Dans **ID client**, entrez l’ID client de l’application LinkedIn que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

## <a name="migration-from-v10-to-v20"></a>Migration de v1.0 vers v2.0

Dernièrement, LinkedIn a [mis à jour ses API v1.0 vers v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Dans le cadre de la migration, Azure AD B2C n’est en mesure d’obtenir le nom complet de l’utilisateur LinkedIn lors du processus d'inscription. Si une adresse e-mail figure parmi les attributs collectés au moment de l'inscription, l’utilisateur doit manuellement entrer l’adresse e-mail et la valider.
