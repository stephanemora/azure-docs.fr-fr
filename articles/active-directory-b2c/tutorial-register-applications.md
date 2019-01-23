---
title: Tutoriel - Inscrire vos applications dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment inscrire vos applications dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248721"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Tutoriel : Inscrire vos applications dans Azure Active Directory B2C

Pour que vos [applications](active-directory-b2c-apps.md) puissent interagir avec Azure Active Directory (Azure AD) B2C, elles doivent être inscrites dans un locataire que vous gérez. Ce tutoriel vous montre comment inscrire des applications à l’aide du portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Inscrire une application web
> * Inscrire une API web
> * Inscrire une application mobile ou native

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire existant.

## <a name="register-a-web-application"></a>Inscrire une application web

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.

    ![Basculer vers l’annuaire de l’abonnement](./media/tutorial-register-applications/switch-directories.png)

2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Sélectionnez **Applications**, puis **Ajouter**.

    ![Ajouter l’application](./media/tutorial-register-applications/add-application.png)

4. Entrez un nom pour l’application. Par exemple, *webapp1*.
5. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
6. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Par exemple, vous pouvez la définir pour écouter localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé à changer par la suite. Dans le cadre des tests, vous pouvez la définir sur `https://jwt.ms`, qui affiche le contenu d’un jeton pour inspection. Pour ce tutoriel, nous utilisons `https://jwt.ms`. 

    L’URL de réponse doit commencer par le schéma `https` et toutes les valeurs d’URL de réponse doivent partager un même domaine DNS. Par exemple, si l’application a l’URL de réponse `https://login.contoso.com`, vous pouvez lui ajouter une partie comme `https://login.contoso.com/new`. Sinon, vous pouvez référencer un sous-domaine DNS de `login.contoso.com`, comme `https://new.login.contoso.com`. Si vous voulez avoir une application avec les URL de réponse `login-east.contoso.com` et `login-west.contoso.com`, vous devez ajouter ces URL de réponse dans cet ordre : `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Vous pouvez ajouter les deux dernières, car il s’agit de sous-domaines de la première URL de réponse, `contoso.com`.

7. Cliquez sur **Créer**.

    ![Définir les propriétés de l’application](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Créer une clé secrète client

Si votre application échange un code contre un jeton, vous devez créer un secret d’application.

1. Sélectionnez **Clés**, puis cliquez sur **Générer une clé**.

    ![Générer des clés](./media/tutorial-register-applications/generate-keys.png)

2. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.

    ![Enregistrer la clé](./media/tutorial-register-applications/save-key.png)
    
3. Sélectionnez **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

    ![Configurer l’accès d’API](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Inscrire une API web

1. Sélectionnez **Applications**, puis **Ajouter**.
3. Entrez un nom pour l’application. Par exemple, *webapi1*.
4. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
5. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Par exemple, vous pouvez le définir pour qu’il écoute localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé que vous modifierez ultérieurement.
6. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
7. Cliquez sur **Créer**.
8. Sélectionnez l’application *webapi1* que vous avez créée, puis sélectionnez **Étendues publiées** pour ajouter des étendues en fonction de vos besoins. Par défaut, l’étendue `user_impersonation` est définie. L’étendue `user_impersonation` permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Si vous le souhaitez, l’étendue `user_impersonation` peut être supprimée.

    ![Définir des étendues publiées](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Inscrire une application mobile ou native

1. Sélectionnez **Applications**, puis **Ajouter**.
2. Entrez un nom pour l’application. Par exemple, *nativeapp1*.
3. Pour **Inclure une application/API web**, sélectionnez **Non**.
4. Pour **Inclure un client natif**, sélectionnez **Oui**.
5. Pour l’**URI de redirection**, entrez un URI de redirection avec un schéma personnalisé. Deux points importants sont à prendre en compte quand vous choisissez un URI de redirection :

    - **Unique** : Le schéma de l’URI de redirection doit être unique pour chaque application. Dans l’exemple `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l’utilisateur fait un choix inapproprié, la connexion échoue.
    - **Complet** : L’URI de redirection doit avoir un schéma et un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//contoso/` fonctionne et `//contoso` échoue. Veillez à ce que l’URI de redirection n’ait pas de caractères spéciaux comme des traits de soulignement.

6. Cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire une application web
> * Inscrire une API web
> * Inscrire une application mobile ou native

> [!div class="nextstepaction"]
> [Créer des flux d’utilisateur dans Azure Active Directory B2C](tutorial-create-user-flows.md)