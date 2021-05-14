---
title: Nettoyer les ressources et supprimer un locataire - Azure Active Directory B2C
description: Étapes décrivant comment supprimer un locataire Azure AD B2C. Découvrez comment supprimer toutes les ressources de locataire, puis le locataire lui-même.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5935847c4abdf3bc9da60937096a19d44fb2d09d
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107982015"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>Nettoyer les ressources et supprimer le locataire

Une fois les tutoriels Azure AD B2C terminés, vous pouvez supprimer le locataire que vous avez utilisé à des fins de test ou de formation. Pour supprimer le locataire, vous devez d’abord supprimer toutes ses ressources. Dans cet article, vous allez :

> [!div class="checklist"]
> * Utilisez l’option **Supprimer le locataire** pour identifier les tâches de nettoyage
> * Supprimer les ressources du locataire (flux d’utilisateur, fournisseurs d’identité, applications, utilisateurs)
> * Supprimer le locataire

## <a name="identify-cleanup-tasks"></a>Identifier les tâches de nettoyage

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.

2. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.

3. Sélectionnez le service **Azure Active Directory**.

4. Sous **Gérer**, sélectionnez **Propriétés**.

5. Sous **Gestion de l’accès pour les ressources Azure**, sélectionnez **Oui**, puis **Enregistrer**.

6. Déconnectez-vous du portail Microsoft Azure, puis reconnectez-vous pour actualiser votre accès. Utilisez à nouveau le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.

7. Sélectionnez le service **Azure Active Directory**.

8. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer le locataire**. La colonne **Action requise** indique les ressources que vous devez supprimer avant de pouvoir supprimer le locataire.

   ![Tâches de suppression de locataire](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>Supprimer les ressources du locataire

Si vous avez ouvert la page de confirmation à partir de la section précédente, vous pouvez utiliser les liens de la colonne **Action requise** pour ouvrir les pages du portail Azure où vous pouvez supprimer ces ressources. Ou vous pouvez supprimer des ressources de locataire dans le service Azure AD B2C en procédant comme suit.

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.

2. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.

3. Sélectionnez le service **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.

4. Supprimez tous les utilisateurs *à l’exception* du compte administrateur auquel vous êtes actuellement connecté : sous **Gérer**, sélectionnez **Utilisateurs**. Dans la page **Tous les utilisateurs**, activez la case à cocher en regard de chaque utilisateur (à l’exception du compte administrateur auquel vous êtes actuellement connecté). Sélectionnez **Supprimer**, puis **Oui** à l’invite.

   ![Suppression d’utilisateurs](media/tutorial-delete-tenant/delete-users.png)
   
5. Supprimez les inscriptions d’applications et *b2c-extensions-app* : sous **Gérer**, sélectionnez **Inscriptions d’applications**. Sélectionnez l’onglet **Toutes les applications**. Choisissez une application, puis **Supprimer**. Répétez cette opération pour toutes les applications, y compris l’application **b2c-extensions-app**.

   ![Supprimer l’application](media/tutorial-delete-tenant/delete-applications.png)

6. Supprimez tous les fournisseurs d’identité que vous avez configurés : sous **Gérer**, sélectionnez **Fournisseurs d’identité**. Sélectionnez un fournisseur d’identité que vous avez configuré, puis **Supprimer**.

   ![Supprimer le fournisseur d’identité](media/tutorial-delete-tenant/identity-providers.png)

8. Supprimez les flux d’utilisateur : sous **Stratégies**, sélectionnez **Flux d’utilisateurs**. En regard de chaque flux d’utilisateur, sélectionnez les points de suspension (...), puis **Supprimer**.

   ![Supprimer les flux d’utilisateur](media/tutorial-delete-tenant/user-flow.png)

9. Supprimez les clés de stratégie : Sous **Stratégies**, sélectionnez **Identity Experience Framework**, puis **Clés de stratégie**. En regard de chaque clé de stratégie, sélectionnez les points de suspension (...), puis **Supprimer**.

10. Supprimez les stratégies personnalisées : sous **Stratégies**, sélectionnez **Identity Experience Framework**, **Stratégies personnalisées**, puis supprimez toutes les stratégies.

## <a name="delete-the-tenant"></a>Supprimer le locataire

1. Avec un rôle d’administrateur général ou un compte d’administrateur d’abonnement, connectez-vous au [portail Azure](https://portal.azure.com/). Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.

2. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.

3. Sélectionnez le service **Azure Active Directory**.

4. Si vous ne vous êtes pas encore accordé les autorisations de gestion des accès, procédez comme suit :

   * Sous **Gérer**, sélectionnez **Propriétés**.
   * Sous **Gestion de l’accès pour les ressources Azure**, sélectionnez **Oui**, puis **Enregistrer**.
   * Déconnectez-vous du portail Azure, puis reconnectez-vous pour actualiser votre accès, et sélectionnez le service **Azure Active Directory**.

5. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer le locataire**.

   ![Supprimer le locataire](media/tutorial-delete-tenant/delete-tenant.png)

6. Suivez les instructions à l’écran pour achever le processus.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Supprimer les ressources de votre locataire
> * Supprimer le locataire

Ensuite, apprenez-en davantage sur la prise en main des [flux utilisateur et de stratégies personnalisées](user-flow-overview.md) Azure AD B2C.
