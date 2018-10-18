---
title: 'Didacticiel : créer un locataire Azure Active Directory B2C | Microsoft Docs'
description: Découvrez comment préparer l’inscription de vos applications en créant un locataire Azure Active Directory B2C à l’aide du portail Azure.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7571e5f4d95320ab92fa3b69b0ea1f05ff9c771f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408400"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Didacticiel : créer un locataire Azure Active Directory B2C

Avant que vos applications puissent interagir avec Azure Active Directory (Azure AD) B2C, elles doivent être inscrites dans un locataire que vous gérez.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

Vous découvrirez comment inscrire une application dans le didacticiel suivant.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Vérifiez que vous utilisez l’annuaire qui contient votre abonnement en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui le contient. Il s’agit d’un annuaire différent de celui qui contiendra votre locataire Azure AD B2C.

    ![Basculer vers l’annuaire de l’abonnement](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
4. Recherchez et sélectionnez **Active Directory B2C**, puis cliquez sur **Créer**.
5. Choisissez **Créer un locataire Azure AD B2C**, entrez un nom d’organisation et un nom de domaine initial, qui est utilisé dans le nom du locataire, sélectionnez le pays (vous ne pourrez pas le changer ultérieurement), puis cliquez sur **Créer**.

    ![Créer un locataire](./media/tutorial-create-tenant/create-tenant.png)

    Dans cet exemple, le nom du locataire est contoso0926Tenant.onmicrosoft.com.

6. Dans la page **Créer un locataire B2C ou lier à un locataire existant**, choisissez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**, sélectionnez le locataire que vous avez créé, sélectionnez votre abonnement, cliquez sur **Créer** et entrez un nom pour le groupe de ressources qui contiendra le locataire, sélectionnez l’emplacement, puis cliquez sur **Créer**.
7. Pour commencer à utiliser votre nouveau locataire, vérifiez que vous utilisez l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui le contient.

    ![Basculer vers l’annuaire du locataire](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

> [!div class="nextstepaction"]
> [Activer une application web pour s’authentifier avec des comptes](active-directory-b2c-tutorials-web-app.md)