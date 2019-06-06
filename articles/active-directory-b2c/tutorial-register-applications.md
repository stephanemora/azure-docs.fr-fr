---
title: Didacticiel - Inscrire une application - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment inscrire une application web dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 380fc1633f94f2365162c1a4e4087c9113e5f663
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511943"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Didacticiel : Inscrire une application dans Azure Active Directory B2C

Pour que vos [applications](active-directory-b2c-apps.md) puissent interagir avec Azure Active Directory (Azure AD) B2C, elles doivent être inscrites dans un locataire que vous gérez. Ce tutoriel vous montre comment inscrire une application web à l’aide du portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Inscrire une application web
> * Créer une clé secrète client

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire Azure AD B2C existant.

## <a name="register-a-web-application"></a>Inscrire une application web

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Sélectionnez **Applications**, puis **Ajouter**.
4. Entrez un nom pour l’application. Par exemple, *webapp1*.
5. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
6. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Par exemple, vous pouvez la définir pour écouter localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé à changer par la suite. Dans le cadre des tests, vous pouvez la définir sur `https://jwt.ms`, qui affiche le contenu d’un jeton pour inspection. Pour ce tutoriel, nous utilisons `https://jwt.ms`. 

    L’URL de réponse doit commencer par le schéma `https` et toutes les valeurs d’URL de réponse doivent partager un même domaine DNS. Par exemple, si l’application a l’URL de réponse `https://login.contoso.com`, vous pouvez lui ajouter une partie comme `https://login.contoso.com/new`. Sinon, vous pouvez référencer un sous-domaine DNS de `login.contoso.com`, comme `https://new.login.contoso.com`. Si vous voulez avoir une application avec les URL de réponse `login-east.contoso.com` et `login-west.contoso.com`, vous devez ajouter ces URL de réponse dans cet ordre : `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Vous pouvez ajouter les deux dernières, car il s’agit de sous-domaines de la première URL de réponse, `contoso.com`.

7. Cliquez sur **Créer**.

## <a name="create-a-client-secret"></a>Créer une clé secrète client

Si votre application échange un code pour un jeton, vous devez créer un secret d’application.

1. Sélectionnez **Clés**, puis cliquez sur **Générer une clé**.
2. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire une application web
> * Créer une clé secrète client

> [!div class="nextstepaction"]
> [Créer des flux d’utilisateur dans Azure Active Directory B2C](tutorial-create-user-flows.md)
