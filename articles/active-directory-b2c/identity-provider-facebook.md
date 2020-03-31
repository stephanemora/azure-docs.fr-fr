---
title: Configurer l’inscription et la connexion avec un compte Facebook
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes Facebook dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188271"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Facebook à l’aide d’Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Création d’une application Facebook

Pour utiliser un compte Facebook en tant que [fournisseur d’identité](authorization-code-flow.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer dans votre locataire une application qui le représente. Si vous n’avez pas encore de compte Facebook, vous pouvez en créer un sur [https://www.facebook.com/](https://www.facebook.com/).

1. Connectez-vous à [Facebook pour les développeurs](https://developers.facebook.com/) avec les informations d’identification de votre compte Facebook.
1. Si ce n’est pas déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour cela, sélectionnez **Prise en main** dans le coin supérieur droit de la page, acceptez les politiques de Facebook et suivez les étapes d’inscription.
1. Sélectionnez **Mes applications**, puis **Créer une application**.
1. Entrez un **nom d’affichage** et une **adresse e-mail de contact** valide.
1. Sélectionnez **Créer un ID d'application**. Vous devrez peut-être accepter les politiques de la plateforme Facebook et effectuer une vérification de sécurité en ligne.
1. Sélectionnez **Paramètres** > **Base**.
1. Choisissez une **Catégorie**, par exemple `Business and Pages`. Cette valeur est requise par Facebook, mais elle n’est pas utilisée pour Azure AD B2C.
1. Au bas de la page, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Site web**.
1. Dans **URL du site**, entrez `https://your-tenant-name.b2clogin.com/`, en remplaçant `your-tenant-name` par le nom de votre locataire. Entrez une URL pour l’**URL de stratégie de confidentialité**, par exemple `http://www.contoso.com`. L’URL de stratégie est une page que vous tenez à jour pour fournir des informations de confidentialité pour votre application.
1. Sélectionnez **Enregistrer les modifications**.
1. En haut de la page, copiez la valeur de l’**ID de l’application**.
1. Sélectionnez **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous avez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
1. Cliquez sur le signe plus en regard de la zone **PRODUITS**, puis sélectionnez **Configurer** sous **Connexion Facebook**.
1. Sous **Connexion Facebook**, sélectionnez **Paramètres**.
1. Dans **URI de redirection OAuth valides**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire. Sélectionnez **Enregistrer les modifications** en bas de la page.
1. Pour rendre votre application Facebook disponible sur Azure AD B2C, sélectionnez le sélecteur État dans la partie supérieure droite de la page et **activez-le** pour rendre l’application publique, puis sélectionnez **Changer de mode**.  À ce stade, l’état doit passer de **Développement** à **Production**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configuration d’un compte Facebook en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Facebook**.
1. Saisissez un **Nom**. Par exemple, *Facebook*.
1. Dans **ID client**, entrez l’ID de l’application Facebook que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète d’application que vous avez consignée.
1. Sélectionnez **Enregistrer**.
