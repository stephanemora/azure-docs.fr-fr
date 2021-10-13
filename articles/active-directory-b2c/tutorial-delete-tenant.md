---
title: Nettoyer les ressources et supprimer un locataire - Azure Active Directory B2C
description: Étapes décrivant comment supprimer un locataire Azure AD B2C. Découvrez comment supprimer toutes les ressources de locataire, puis le locataire lui-même.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 277303a2272f7622e5082b06a73980619bf93aa8
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615958"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>Nettoyer les ressources et supprimer le locataire

Une fois les tutoriels Azure AD B2C terminés, vous pouvez supprimer le locataire que vous avez utilisé à des fins de test ou de formation. Pour supprimer le locataire, vous devez d’abord supprimer toutes ses ressources. Dans cet article, vous allez :

> [!div class="checklist"]
> * Utilisez l’option **Supprimer le locataire** pour identifier les tâches de nettoyage
> * Supprimer les ressources du locataire (flux d’utilisateur, fournisseurs d’identité, applications, utilisateurs)
> * Supprimer le locataire

## <a name="identify-cleanup-tasks"></a>Identifier les tâches de nettoyage

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sélectionnez le service **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Propriétés**.
1. Sous **Gestion de l’accès pour les ressources Azure**, sélectionnez **Oui**, puis **Enregistrer**.
1. Déconnectez-vous du portail Microsoft Azure, puis reconnectez-vous pour actualiser votre accès. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sélectionnez le service **Azure Active Directory**.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer le locataire**. La colonne **Action requise** indique les ressources que vous devez supprimer avant de pouvoir supprimer le locataire.

   ![Tâches de suppression de locataire](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>Supprimer les ressources du locataire

Si vous avez ouvert la page de confirmation à partir de la section précédente, vous pouvez utiliser les liens de la colonne **Action requise** pour ouvrir les pages du portail Azure où vous pouvez supprimer ces ressources. Ou vous pouvez supprimer des ressources de locataire dans le service Azure AD B2C en procédant comme suit.

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sélectionnez le service **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Supprimez tous les utilisateurs *à l’exception* du compte administrateur auquel vous êtes actuellement connecté : sous **Gérer**, sélectionnez **Utilisateurs**. Dans la page **Tous les utilisateurs**, activez la case à cocher en regard de chaque utilisateur (à l’exception du compte administrateur auquel vous êtes actuellement connecté). Sélectionnez **Supprimer**, puis **Oui** à l’invite.

   ![Suppression d’utilisateurs](media/tutorial-delete-tenant/delete-users.png)

1. Supprimez les inscriptions d’applications et *b2c-extensions-app* : sous **Gérer**, sélectionnez **Inscriptions d’applications**. Sélectionnez l’onglet **Toutes les applications**. Choisissez une application, puis **Supprimer**. Répétez cette opération pour toutes les applications, y compris l’application **b2c-extensions-app**.

   ![Supprimer l’application](media/tutorial-delete-tenant/delete-applications.png)

1. Supprimez tous les fournisseurs d’identité que vous avez configurés : sous **Gérer**, sélectionnez **Fournisseurs d’identité**. Sélectionnez un fournisseur d’identité que vous avez configuré, puis **Supprimer**.

   ![Supprimer le fournisseur d’identité](media/tutorial-delete-tenant/identity-providers.png)

1. Supprimez les flux d’utilisateur : sous **Stratégies**, sélectionnez **Flux d’utilisateurs**. En regard de chaque flux d’utilisateur, sélectionnez les points de suspension (...), puis **Supprimer**.

   ![Supprimer les flux d’utilisateur](media/tutorial-delete-tenant/user-flow.png)

1. Supprimez les clés de stratégie : Sous **Stratégies**, sélectionnez **Identity Experience Framework**, puis **Clés de stratégie**. En regard de chaque clé de stratégie, sélectionnez les points de suspension (...), puis **Supprimer**.

1. Supprimez les stratégies personnalisées : sous **Stratégies**, sélectionnez **Identity Experience Framework**, **Stratégies personnalisées**, puis supprimez toutes les stratégies.

## <a name="delete-the-tenant"></a>Supprimer le locataire

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sélectionnez le service **Azure Active Directory**.
1. Si vous ne vous êtes pas encore accordé les autorisations de gestion des accès, procédez comme suit :

   * Sous **Gérer**, sélectionnez **Propriétés**.
   * Sous **Gestion de l’accès pour les ressources Azure**, sélectionnez **Oui**, puis **Enregistrer**.
   * Déconnectez-vous du portail Azure, puis reconnectez-vous pour actualiser votre accès, et sélectionnez le service **Azure Active Directory**.

1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer le locataire**.

   ![Supprimer le locataire](media/tutorial-delete-tenant/delete-tenant.png)

1. Suivez les instructions à l’écran pour achever le processus.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Supprimer les ressources de votre locataire
> * Supprimer le locataire

Ensuite, apprenez-en davantage sur la prise en main des [flux utilisateur et de stratégies personnalisées](user-flow-overview.md) Azure AD B2C.
