---
title: 'Tutoriel : Inscrire une application'
titleSuffix: Azure AD B2C
description: Découvrez comment inscrire une application web dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbb689182a45367061ae129304a98a8ee9962051
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840109"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutoriel : Inscrire une application dans Azure Active Directory B2C

Pour que vos [applications](application-types.md) puissent interagir avec Azure Active Directory B2C (Azure AD B2C), elles doivent être inscrites dans un locataire que vous gérez. Ce tutoriel vous montre comment inscrire une application web à l’aide du portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Inscrire une application web
> * Créer une clé secrète client

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire Azure AD B2C existant.

## <a name="register-a-web-application"></a>Inscrire une application web

Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *webapp1*.
1. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
1. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Par exemple, vous pouvez le définir pour qu’il écoute localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé que vous modifierez ultérieurement.

    Pour des besoins de test, comme ce tutoriel, vous pouvez le définir sur `https://jwt.ms`, ce qui affiche le contenu d’un jeton pour inspection. Pour ce tutoriel, définissez l’**URL de réponse** sur `https://jwt.ms`.

    Les restrictions suivantes s’appliquent aux URL de réponse :

    * L’URL de réponse doit commencer par le schéma `https`.
    * L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin, ne spécifiez pas `.../ABC/response-oidc` dans l’URL de réponse. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.

1. Sélectionnez **Créer** pour terminer l’inscription de l’application.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapp1*.
1. Sélectionnez **Comptes dans un annuaire d’organisation ou un fournisseur d’identité**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://jwt.ms` dans la zone de texte de l’URL.

    L’URI de redirection est le point de terminaison auquel l’utilisateur est envoyé par le serveur d’autorisation (dans ce cas, Azure AD B2C) après avoir terminé son interaction avec l’utilisateur, et auquel un jeton d’accès ou un code d’autorisation est envoyé en cas d’autorisation réussie. Dans une application de production, il s’agit généralement d’un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/auth-response`. À des fins de test comme dans ce didacticiel, vous pouvez le définir sur `https://jwt.ms`, une application web Microsoft qui affiche le contenu décodé d’un jeton (le contenu du jeton ne quitte jamais votre navigateur). Pendant le développement d’une application, vous pouvez ajouter le point de terminaison sur lequel votre application écoute localement, comme `https://localhost:5000`. Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier.

    Les restrictions suivantes s’appliquent aux URI de redirection :

    * L’URL de réponse doit commencer par le schéma `https`.
    * L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin, ne spécifiez pas `.../ABC/response-oidc` dans l’URL de réponse. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.

1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.

Une fois l’inscription de l’application terminée, activez le flux d’octroi implicite :

1. Sous **Gérer**, sélectionnez **Authentification**.
1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).
1. Sous **Octroi implicite**, cochez les deux cases **Jetons d’accès** et **Jetons d’ID**.
1. Sélectionnez **Enregistrer**.

* * *

## <a name="create-a-client-secret"></a>Créer une clé secrète client

Si votre application échange un code d’autorisation contre un jeton d’accès, vous devez créer un secret d’application.

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Dans la page **Azure AD B2C – Applications**, sélectionnez l’application que vous avez créée, par exemple *webapp1*.
1. Sélectionnez **Clés**, puis **Générer la clé**.
1. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utiliserez cette valeur comme secret d’application dans le code de votre application.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Dans la page **Azure AD B2C – Inscriptions d’applications**, sélectionnez l’application que vous avez créée, par exemple *webapp1*.
1. Sous **Gérer**, sélectionnez **Certificats et secrets**.
1. Sélectionnez **Nouveau secret client**.
1. Entrez une description pour la clé secrète client dans la zone **Description**. Par exemple, *clientsecret1*.
1. Sous **Expire**, sélectionnez une durée pendant laquelle le secret est valide, puis sélectionnez **Ajouter**.
1. Enregistrez la **Valeur** du secret. Vous utiliserez cette valeur comme secret d’application dans le code de votre application.

* * *

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire une application web
> * Créer une clé secrète client

Ensuite, découvrez comment créer des flux utilisateur pour permettre à vos utilisateurs de s’inscrire, se connecter et gérer leur profil.

> [!div class="nextstepaction"]
> [Créer des flux utilisateur dans Azure Active Directory B2C >](tutorial-create-user-flows.md)
