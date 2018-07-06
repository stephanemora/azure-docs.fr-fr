---
title: 'Didacticiel : créer un locataire Azure Active Directory B2C | Microsoft Docs'
description: Découvrez comment préparer l’inscription de vos applications en créant un locataire Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 20865fc3adf8610b5a4ce111e3db91aef714fdd6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448303"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Didacticiel : créer un locataire Azure Active Directory B2C

Avant que vos applications puissent interagir avec Azure Active Directory (Azure AD) B2C, elles doivent être inscrites dans un locataire que vous gérez.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

Vous découvrirez comment inscrire une application dans le didacticiel suivant.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

1. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Dans la zone de recherche au-dessus de la liste des ressources de la Place de marché Azure, recherchez et sélectionnez **Active Directory B2C**, puis cliquez sur **Créer**.
3. Choisissez **Créer un locataire Azure AD B2C**, entrez un nom d’organisation et un nom de domaine initial, qui est utilisé dans le nom du locataire, sélectionnez le pays, puis cliquez sur **Créer**. Soyez sûr du pays du locataire, car il ne peut pas être modifié ultérieurement.

    ![Créer un client](./media/tutorial-create-tenant/create-tenant.png)

    Dans cet exemple, le nom du locataire est contoso0522Tenant.onmicrosoft.com

Pour commencer à gérer votre nouveau locataire, cliquez sur le mot **ici** qui signifie **cliquez ici pour gérer votre nouveau répertoire**. Un message de **dépannage** indiquant que vous devez lier votre abonnement au nouveau locataire s’affiche. 

## <a name="link-your-tenant-to-your-subscription"></a>Lier votre locataire à votre abonnement

Vous devez lier votre locataire Azure AD B2C à votre abonnement Azure pour activer toutes les fonctionnalités et payer les frais d’utilisation. Si vous ne liez le locataire à votre abonnement, vos applications ne fonctionneront pas correctement.

1. Assurez-vous que vous utilisez le répertoire qui contient l’abonnement que vous souhaitez associer au nouveau locataire en activant le répertoire dans le coin supérieur droit du portail Azure.

    ![Changer de répertoires](./media/tutorial-create-tenant/switch-directories.png)

    Puis en sélectionnant le répertoire qui contient votre abonnement.

    ![Sélectionner le répertoire](./media/tutorial-create-tenant/select-directory.png)

2. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
3. Dans la zone de recherche au-dessus de la liste des ressources de la Place de marché Azure, recherchez et sélectionnez **Active Directory B2C**, puis cliquez sur **Créer**.
4. Choisissez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**, sélectionnez le locataire que vous avez créé, sélectionnez votre abonnement, entrez *myContosoTenantRG* pour le nom de groupe de ressources, acceptez l’emplacement, puis cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement

> [!div class="nextstepaction"]
> [Activer une application web pour s’authentifier avec des comptes](active-directory-b2c-tutorials-web-app.md)