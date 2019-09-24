---
title: Tutoriel – Créer un locataire Azure Active Directory B2C
description: Découvrez comment préparer l’inscription de vos applications en créant un locataire Azure Active Directory B2C à l’aide du portail Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063347"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Didacticiel : Créer un locataire Azure Active Directory B2C

Pour que vos applications puissent interagir avec Azure Active Directory B2C (Azure AD B2C), elles doivent être inscrites dans un locataire que vous gérez.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

Vous découvrirez comment inscrire une application dans le didacticiel suivant.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre abonnement. Sélectionnez le filtre **Annuaire + abonnement** dans le menu supérieur, puis sélectionnez l’annuaire qui contient votre abonnement. Cet annuaire est différent de celui qui contiendra votre locataire Azure AD B2C.

    ![Filtre Répertoire et abonnement avec locataire d'abonnement sélectionné](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
4. Recherchez et sélectionnez **Active Directory B2C**, puis cliquez sur **Créer**.
5. Choisissez **Créer un locataire Azure AD B2C** et entrez un nom d’organisation et un nom de domaine initial. Sélectionnez le pays/région (qui peut être modifié(e) par la suite), puis cliquez sur **Créer**.

    Le nom de domaine initial est intégré à votre nom de locataire. Dans cet exemple, le nom de locataire est *contoso0926Tenant.onmicrosoft.com* :

    ![Page de création de locataire B2C du Portail Azure](./media/tutorial-create-tenant/create-tenant.PNG)

6. Dans la page **Créer un locataire B2C ou lier à un locataire existant** , choisissez **Lier un locataire Azure AD B2C existant à mon abonnement Azure** .

    Sélectionnez le locataire que vous avez créé, puis sélectionnez votre abonnement.

    Pour le groupe de ressources, sélectionnez **Créer nouveau**. Entrez un nom pour le groupe de ressources qui contiendra le locataire, sélectionnez l’emplacement, cliquez sur **Créer**.
1. Pour commencer à utiliser votre nouveau locataire, vérifiez que vous utilisez l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui le contient.

    Si vous ne voyez pas dans un premier temps votre nouveau locataire Azure B2C dans la liste, actualisez la fenêtre du navigateur, puis sélectionnez à nouveau le filtre **Annuaire + abonnement** dans le menu du haut.

    ![Filtre Répertoire et abonnement avec locataire B2C sélectionné](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

Ensuite, découvrez comment inscrire une application web dans votre nouveau locataire.

> [!div class="nextstepaction"]
> [Inscrire vos applications >](tutorial-register-applications.md)
