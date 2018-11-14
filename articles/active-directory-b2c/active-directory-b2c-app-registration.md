---
title: Inscrire une application dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment inscrire votre application auprès d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013409"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Inscrire une application dans Azure Active Directory B2C

Pour générer une [application](active-directory-b2c-apps.md) acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure AD B2C. Cet article vous aide à inscrire une application dans un client B2C Azure Active Directory (Azure AD) en quelques minutes. Lorsque vous avez terminé, votre application est inscrite comme utilisable dans le client Azure AD B2C.

## <a name="prerequisites"></a>Prérequis

Obtenez votre propre client en suivant la procédure décrite dans [Créer un client Azure Active Directory B2C](tutorial-create-tenant.md).

Choisissez les étapes suivantes en fonction du type de votre application :

- [Inscrire une application web](#register-a-web-application)
- [Inscrire une API web](#register-a-web-api)
- [Inscrire une application mobile ou native](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Inscrire une application web

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Sélectionnez **Applications**, puis **Ajouter**.
4. Entrez un nom pour l’application. Par exemple *testapp1*.
5. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
6. Pour les **URL de réponse**, entrez les points de terminaison où Azure AD B2C doit retourner des jetons requis par votre application. Par exemple, vous pouvez le définir pour qu’il écoute localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé que vous modifierez ultérieurement.
7. Cliquez sur **Créer**.

### <a name="create-a-client-secret"></a>Créer une clé secrète client

Si votre application appelle une API web sécurisée par Azure AD B2C, vous devez créer un secret d’application.

1. Sélectionnez **Clés**, puis cliquez sur **Générer une clé**. 
2. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.
3. Sélectionnez **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

## <a name="register-a-web-api"></a>Inscrire une API web

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Sélectionnez **Applications**, puis **Ajouter**.
4. Entrez un nom pour l’application. Par exemple *testapp2*.
5. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
6. Pour les **URL de réponse**, entrez les points de terminaison où Azure AD B2C doit retourner des jetons requis par votre application. Par exemple, vous pouvez le définir pour qu’il écoute localement sur `https://localhost:44316`. Si vous ne connaissez pas encore le numéro de port, vous pouvez entrer une valeur d’espace réservé que vous modifierez ultérieurement.
7. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
8. Cliquez sur **Créer**.
9. Sélectionnez **Étendues publiées** pour ajouter des étendues si nécessaire. Par défaut, l’étendue `user_impersonation` est définie. L’étendue `user_impersonation` permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Si vous le souhaitez, l’étendue `user_impersonation` peut être supprimée.

## <a name="register-a-mobile-or-native-application"></a>Inscrire une application mobile ou native

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Sélectionnez **Applications**, puis **Ajouter**.
4. Entrez un nom pour l’application. Par exemple *testapp3*.
5. Pour **Inclure une application/API web**, sélectionnez **Non**.
6. Pour **Inclure un client natif**, sélectionnez **Oui**.
7. Pour **URI de redirection**, entrez un [URI de redirection avec un schéma personnalisé](active-directory-b2c-apps.md). Prenez soin de choisir un URI de redirection approprié et de ne pas y inclure de caractères spéciaux tels que des traits de soulignement.
8. Cliquez sur **Créer**.

### <a name="create-a-client-secret"></a>Créer une clé secrète client

Si votre application appelle une API web sécurisée par Azure AD B2C, vous devez créer un secret d’application.

1. Sélectionnez **Clés**, puis cliquez sur **Générer une clé**. 
2. Sélectionnez **Enregistrer** pour afficher la clé. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.
3. Sélectionnez **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un des [didacticiels de démarrage rapide](active-directory-b2c-overview.md) afin de devenir opérationnel.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET avec inscription, connexion et réinitialisation de mot de passe](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
