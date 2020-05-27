---
title: Ajouter Facebook en tant que fournisseur d’identité – Azure AD
description: Configurez une fédération avec Facebook pour permettre à des utilisateurs externes (invités) de se connecter à vos applications Azure AD avec leurs propres comptes Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92fbd254f223e2c7eb70a4e86bb7e904294395e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595078"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Ajouter Facebook en tant que fournisseur d’identité pour les identités externes

Vous pouvez ajouter Facebook à vos flux utilisateur d’inscription en libre-service (préversion) afin que les utilisateurs puissent se connecter à vos applications en utilisant leurs propres comptes Facebook. Pour permettre aux utilisateurs de se connecter à l’aide de Facebook, vous commencer par [activer l’inscription en libre-service](self-service-sign-up-user-flow.md) pour votre locataire. Après avoir ajouté Facebook en tant que fournisseur d’identité, configurez un flux utilisateur pour l’application, puis sélectionnez Facebook comme l’une des options de connexion.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Créer une application dans la console des développeurs Facebook

Pour utiliser un compte Facebook en tant que [fournisseur d’identité](identity-providers.md), vous devez créer une application dans la console des développeurs Facebook. Si vous n’avez pas encore de compte Facebook, vous pouvez en créer un sur [https://www.facebook.com/](https://www.facebook.com).

> [!NOTE]  
> Utilisez les URL suivantes aux étapes 9 et 16 ci-dessous.
> - Pour **URL du site** entrez `https://login.microsoftonline.com`.
> - Pour **URI de redirection OAuth valides**, entrez `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`. Vous trouverez votre ID `<tenant-ID>` dans le panneau Vue d’ensemble d’Azure Active Directory.


1. Connectez-vous à [Facebook pour les développeurs](https://developers.facebook.com/) avec les informations d’identification de votre compte Facebook.
2. Si ce n’est pas déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour cela, sélectionnez **Prise en main** dans le coin supérieur droit de la page, acceptez les politiques de Facebook et suivez les étapes d’inscription.
3. Sélectionnez **Mes applications**, puis **Créer une application**.
4. Entrez un **nom d’affichage** et une **adresse e-mail de contact** valide.
5. Sélectionnez **Créer un ID d'application**. Vous devrez peut-être accepter les politiques de la plateforme Facebook et effectuer une vérification de sécurité en ligne.
6. Sélectionnez **Paramètres** > **Base**.
7. Choisissez une **catégorie**, par exemple, Entreprise et pages. Cette valeur est requise par Facebook, mais n’est pas utilisée pour Azure AD.
8. Au bas de la page, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Site web**.
9. Dans **URL du site**, entrez l’URL appropriée (indiquée ci-dessus).
10. Dans **URL de la politique de confidentialité**, entrez l’URL de la page dans laquelle vous gérez les informations de confidentialité de votre application, par exemple `http://www.contoso.com`.
11. Sélectionnez **Enregistrer les modifications**.
12. En haut de la page, copiez la valeur de l’**ID de l’application**.
13. Sélectionnez **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous avez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
14. Cliquez sur le signe plus en regard de la zone **PRODUITS**, puis sélectionnez **Configurer** sous **Connexion Facebook**.
15. Sous **Connexion Facebook**, sélectionnez **Paramètres**.
16. Dans **URI de redirection OAuth valides**, entrez l’URL appropriée (indiquée ci-dessus).
17. Sélectionnez **Enregistrer les modifications** en bas de la page.
18. Pour rendre votre application Facebook disponible sur Azure AD, sélectionnez le sélecteur État dans la partie supérieure droite de la page et **activez-le** pour rendre l’application publique, puis sélectionnez **Changer de mode**. À ce stade, l’état doit passer de **Développement** à **Production**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configuration d’un compte Facebook en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre locataire Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Tous les fournisseurs d’identité**, puis **Facebook**.
5. Dans **ID client**, entrez l’**ID** de l’application Facebook que vous avez créée précédemment.
6. Dans **Clé secrète client**, entrez la **Clé secrète d’application** que vous avez consignée.

   ![Capture d’écran montrant la page Sélectionner un fournisseur d’identité sociale](media/facebook-federation/add-social-identity-provider-page.png)

7. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- [Inviter des utilisateurs externes à collaborer](add-users-administrator.md)
- [Ajouter l’inscription en libre-service à une application](self-service-sign-up-user-flow.md)
