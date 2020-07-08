---
title: Configurer l’inscription et la connexion avec un compte Google
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Google dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 72e4de1473766d50512453ae38b6033ff0c5b73d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388032"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C

## <a name="create-a-google-application"></a>Créer une application Google

Pour utiliser un compte Google en tant que [fournisseur d’identité](authorization-code-flow.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans votre Console Google Developers. Si vous ne disposez pas déjà d’un compte Google, vous pouvez vous inscrire à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Connectez-vous à la [Console Google Developers](https://console.developers.google.com/) avec les informations d’identification de votre compte Google.
1. Dans le coin supérieur gauche de la page, sélectionnez la liste des projets, puis **Nouveau projet**.
1. Entrez un **nom de projet** , sélectionnez **Créer**.
1. Vérifiez que vous utilisez le nouveau projet en sélectionnant la liste déroulante projet en haut à gauche de l’écran, sélectionnez votre projet par nom, puis sélectionnez **Ouvrir**.
1. Sélectionnez **Écran de consentement OAuth** dans le menu de gauche, sélectionnez **Externe**, puis **Créer**.
Entrez un **nom** pour votre application. Entrez *b2clogin.com* dans la section des **Domaines autorisés** et sélectionnez **Enregistrer**.
1. Sélectionnez **Informations d’identification** dans le menu de gauche, puis **Créer des informations d’identification** > **ID client OAuth**.
1. Sous **Type d’application**, sélectionnez **Application web**.
1. Entrez un **Nom** pour votre application, saisissez `https://your-tenant-name.b2clogin.com` dans **Origines JavaScript autorisées** et `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URI de redirection autorisés**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
1. Cliquez sur **Créer**.
1. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google comme fournisseur d’identité dans votre locataire. **Client secret** est une information d’identification de sécurité importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurer un compte Google comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Google**.
1. Saisissez un **Nom**. Par exemple, *Google*.
1. Dans **ID client**, entrez l’ID client de l’application Google que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
