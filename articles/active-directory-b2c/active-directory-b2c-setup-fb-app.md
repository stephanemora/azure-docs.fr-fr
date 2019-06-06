---
title: Configurer l’inscription et la connexion avec un compte Facebook - Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Facebook dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b90ba89f17b42f4d92c666c3f539fcad3e3227c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733527"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Facebook à l’aide d’Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Création d’une application Facebook

Pour utiliser un compte Facebook en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Facebook, vous pouvez en obtenir un à l’adresse [https://www.facebook.com/](https://www.facebook.com/).

1. Connectez-vous à [Facebook pour les développeurs](https://developers.facebook.com/) avec les informations d’identification de votre compte Facebook.
2. Si ce n’est pas déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour ce faire, sélectionnez **prise en main** sur le coin supérieur droit de la page, acceptez les politiques de Facebook et effectuez les étapes de l’inscription.
3. Sélectionnez **mes applications** , puis **ajouter une nouvelle application**.
4. Entrez un **nom d’affichage** et une **adresse e-mail de contact** valide.
5. Cliquez sur **Créer l’ID d’application**. Vous devrez peut-être accepter les politiques de la plateforme Facebook et effectuer une vérification de sécurité en ligne.
6. Sélectionnez **Paramètres** > **Base**.
7. Choisissez une **Catégorie**, par exemple `Business and Pages`. Cette valeur est requise par Facebook, mais elle n’est pas utilisée pour Azure AD B2C.
8. Au bas de la page, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Site web**.
9. Dans **URL du site**, entrez `https://your-tenant-name.b2clogin.com/`, en remplaçant `your-tenant-name` par le nom de votre locataire. Entrez une URL pour l’**URL de stratégie de confidentialité**, par exemple `http://www.contoso.com`. L’URL de stratégie est une page que vous tenez à jour pour fournir des informations de confidentialité pour votre application.
10. Sélectionnez **Enregistrer les modifications**.
11. En haut de la page, copiez la valeur de l’**ID de l’application**.
12. Cliquez sur **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous avez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
13. Sélectionnez le signe plus en regard **produits**, puis sélectionnez **configurer** sous **connexion Facebook**.
14. Sous **connexion Facebook**, sélectionnez **paramètres**.
15. Dans **URI de redirection OAuth valides**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire. Cliquez sur **Save Changes** en bas de la page.
16. Pour rendre votre application Facebook disponible dans Azure AD B2C, cliquez sur le sélecteur d’état dans la partie supérieure droite de la page et **activez**-le pour rendre l’application publique, puis cliquez sur **Confirmer**.  À ce stade, l’état doit passer de **Développement** à **Production**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configuration d’un compte Facebook en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *Facebook*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’identifiant d’application enregistrée précédemment en tant qu’**ID Client** et entrez le secret d’application enregistré en tant que **Secret du client** de l’application Facebook créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Facebook.
