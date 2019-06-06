---
title: 'Didacticiel : créer un locataire Azure Active Directory B2C | Microsoft Docs'
description: Découvrez comment préparer l’inscription de vos applications en créant un locataire Azure Active Directory B2C à l’aide du portail Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e2568bca8f8ecf170c82c5388823193b8b0457cf
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734460"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Didacticiel : Créer un locataire Azure Active Directory B2C

Avant que vos applications puissent interagir avec Azure Active Directory (Azure AD) B2C, elles doivent être inscrites dans un locataire que vous gérez.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

Vous découvrirez comment inscrire une application dans le didacticiel suivant.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Vérifiez que vous utilisez l’annuaire qui contient votre abonnement en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui le contient. Ce répertoire est différent de celui qui contiendra votre locataire Azure AD B2C.

    ![Basculer vers l’annuaire de l’abonnement](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
4. Recherchez et sélectionnez **Active Directory B2C**, puis cliquez sur **Créer**.
5. Choisissez **créer un locataire Azure AD B2C**, entrez un nom d’organisation et le nom de domaine initial, qui est utilisé dans le nom du client, sélectionnez la pays/région (il ne peut pas être modifié ultérieurement), puis cliquez sur **créer** .

    ![Créer un client](./media/tutorial-create-tenant/create-tenant.png)

    Dans cet exemple, le nom du locataire est contoso0926Tenant.onmicrosoft.com

6. Sur la page **Créer un locataire B2C ou Lier à un locataire existant**, choisissez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**, sélectionnez le locataire que vous avez créé, sélectionnez votre abonnement, puis cliquez sur **Créer**.
7. Entrez un nom pour le groupe de ressources qui contiendra le locataire, sélectionnez l’emplacement, cliquez sur **Créer**.
8. Pour commencer à utiliser votre nouveau locataire, vérifiez que vous utilisez l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui le contient.

    ![Basculer vers l’annuaire du locataire](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

> [!div class="nextstepaction"]
> [Inscrire vos applications](tutorial-register-applications.md)
