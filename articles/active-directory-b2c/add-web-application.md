---
title: Ajouter une application web - Azure Active Directory B2C | Microsoft Docs
description: Apprenez à ajouter une application web à votre locataire Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ede3fd0dd1d0351e691a9f160260c029d01c8f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704025"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Ajouter une application d'API web à votre locataire Azure Active Directory B2C

 Inscrire les ressources de l’API Web dans votre client afin qu’ils peuvent accepter et répondre aux requêtes par les applications clientes qui présentent un jeton d’accès. Cet article vous montre comment inscrire l’application dans Azure Active Directory (Azure AD) B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Vérifiez que vous utilisez le répertoire qui contient votre locataire Azure AD B2C. Sélectionnez le **filtre Directory et abonnement** dans le menu supérieur et sélectionnez le répertoire qui contient votre client.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application. Par exemple, *webapi1*.
6. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
7. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans votre application de production, vous pouvez définir l’URL de réponse à une valeur comme `https://localhost:44332`. À des fins de test, la valeur est l’URL de réponse `https://jwt.ms`.
8. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
9. Cliquez sur **Créer**.
10. Sur la page des propriétés, enregistrez l'ID d'application que vous utiliserez pour configurer l'application web.

## <a name="configure-scopes"></a>Configurer des étendues

Les étendues permettent de gérer l'accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, les utilisateurs de l’API web peuvent avoir un accès en lecture et en écriture, ou les utilisateurs de l’API web peuvent avoir l’accès en lecture uniquement. Dans ce didacticiel, vous allez utiliser des étendues pour définir des autorisations d’accès en lecture et en écriture pour l’API web.

1. Sélectionnez **Applications**, puis *webapi1*.
2. Sélectionnez **Étendues publiées**.
3. Pour **Étendue**, entrez `Read`, puis pour la description, entrez `Read access to the application`.
4. Pour **Étendue**, entrez `Write`, puis pour la description, entrez `Write access to the application`.
5. Cliquez sur **Enregistrer**.

Les étendues publiées peuvent être utilisées pour accorder à une application cliente l'autorisation d'accéder à l'API web.

## <a name="grant-permissions"></a>Accorder des autorisations

Pour appeler une API web protégée à partir d'une application, vous devez accorder à cette application les autorisations d'accès à l'API. Par exemple, dans [Tutoriel : Inscrire une application dans Azure Active Directory B2C](tutorial-register-applications.md), une application web est créée dans Azure AD B2C nommée *webapp1*. Vous pouvez utiliser cette application pour appeler l'API web.

1. Sélectionnez **Applications**, puis votre application web.
2. Sélectionnez **Accès aux API**, puis **Ajouter**.
3. Dans la liste déroulante **Sélectionner une API**, sélectionnez *webapi1*.
4. Dans la liste déroulante **Sélectionner des étendues**, sélectionnez les étendues **Lecture** et **Écriture** que vous avez définies précédemment.
5. Cliquez sur **OK**.

Votre application est inscrite pour appeler l'API web protégée. Un utilisateur s'authentifie auprès d'Azure AD B2C pour utiliser l'application. L'application obtient d'Azure AD B2C l'autorisation d'accéder à l'API web protégée.
