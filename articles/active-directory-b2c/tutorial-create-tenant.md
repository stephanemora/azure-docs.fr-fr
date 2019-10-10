---
title: Tutoriel – Créer un locataire Azure Active Directory B2C
description: Découvrez comment préparer l’inscription de vos applications en créant un locataire Azure Active Directory B2C à l’aide du portail Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345213"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Didacticiel : Créer un locataire Azure Active Directory B2C

Pour que vos applications puissent interagir avec Azure Active Directory B2C (Azure AD B2C), elles doivent être inscrites dans un locataire que vous gérez.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement
> * Passer à l’annuaire contenant votre locataire Azure AD B2C
> * Ajouter la ressource Azure AD B2C en tant que *favori* dans le portail Azure

Vous découvrirez comment inscrire une application dans le didacticiel suivant.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre abonnement.

    Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre abonnement. Cet annuaire est différent de celui qui contiendra votre locataire Azure AD B2C.

    ![Filtre Annuaire et abonnement avec locataire d’abonnement sélectionné](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
1. Recherchez et sélectionnez **Active Directory B2C**, puis sélectionnez **Créer**.
1. Cliquez sur **Créer un nouveau client Azure Active Directory B2C**.

    ![Créer un locataire Azure AD B2C sélectionné dans le portail Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Entrez des valeurs pour **Nom de l’organisation** et **Nom de domaine initial**. Sélectionnez une valeur pour **Pays ou région** (que vous pouvez changer par la suite), puis **Créer**.

    Le nom de domaine est utilisé dans le cadre du nom de domaine de locataire complet. Dans cet exemple, le nom du locataire est *contosob2c.onmicrosoft.com* :

    ![Formulaire de création de locataire avec des exemples de valeurs dans le portail Azure](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Une fois la création du locataire terminée, sélectionnez le lien **Créer un locataire B2C ou lier à un locataire existant** en haut de la page de création du locataire.

    ![Lien de navigation de liaison à un locataire mis en surbrillance dans le portail Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Sélectionnez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**.

   ![Lier une sélection d’abonnement existante dans le portail Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Sélectionnez le **Locataire Azure AD B2C** que vous avez créé, puis votre **abonnement**.

    Pour **Groupe de ressources**, sélectionnez **Créer**. Entrez un **Nom** pour le groupe de ressources qui contiendra le locataire, sélectionnez l’**Emplacement du groupe de ressources**, puis **Créer**.

    ![Formulaire de liaison des paramètres d’abonnement dans le portail Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>Sélectionner l’annuaire de votre locataire B2C

Pour commencer à utiliser votre nouveau locataire Azure AD B2C, vous devez passer à l’annuaire qui contient le locataire.

Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur du portail Azure, puis l’annuaire qui contient votre locataire Azure AD B2C.

Si vous ne voyez pas dans un premier temps votre nouveau locataire Azure B2C dans la liste, actualisez la fenêtre du navigateur, puis sélectionnez à nouveau le filtre **Annuaire + abonnement** dans le menu du haut.

![Annuaire contenant le locataire B2C sélectionné dans le portail Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Ajouter Azure AD B2C en tant que favori (facultatif)

Cette étape facultative vous permet de sélectionner plus facilement votre locataire Azure AD B2C dans les tutoriels suivants et ultérieurs.

Au lieu de rechercher « Azure AD B2C » dans **Tous les services** chaque fois que vous souhaitez utiliser votre locataire, vous pouvez à la place marquer la ressource comme favori. Vous pouvez ensuite la sélectionner dans le menu **Favoris** de gauche pour accéder rapidement à votre locataire Azure AD B2C.

Vous ne devez effectuer cette opération qu’une seule fois. Avant d’effectuer ces étapes, assurez-vous d’avoir basculé vers l’annuaire contenant votre locataire Azure AD B2C comme décrit dans la section précédente, [Sélectionner l’annuaire de votre locataire B2C](#select-your-b2c-tenant-directory).

1. Sélectionnez **Tous les services** dans le menu de gauche du [portail Azure](https://portal.azure.com)
1. Entrez *Azure AD B2C* dans la zone de texte de recherche
1. Sélectionnez l’**étoile** pour ajouter Azure AD B2C à vos favoris
1. *Azure AD B2C* apparaît maintenant dans le menu de gauche **Favoris**. Vous pouvez ensuite le sélectionner et le faire glisser vers le haut de la liste, si vous le souhaitez, comme le montre l’image suivante :

![Étapes à suivre pour ajouter Azure AD B2C en tant que favori dans le portail Azure](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement
> * Passer à l’annuaire contenant votre locataire Azure AD B2C
> * Ajouter la ressource Azure AD B2C en tant que *favori* dans le portail Azure

Ensuite, découvrez comment inscrire une application web dans votre nouveau locataire.

> [!div class="nextstepaction"]
> [Inscrire vos applications >](tutorial-register-applications.md)
