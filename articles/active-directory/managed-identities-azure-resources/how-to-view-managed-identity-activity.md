---
title: Afficher les activités de mise à jour et de connexion pour les identités managées
description: Instructions pas à pas pour l’affichage des activités effectuées sur les identités managées et des authentifications effectuées par les identités managées
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ab5ed10dcb9127c5b62850228d22222b4225be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814420"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>Afficher les activités de mise à jour et de connexion pour les identités managées

Cet article explique comment afficher les mises à jour effectuées sur les identités managées et les tentatives de connexion effectuées par les identités managées.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous pour créer un compte gratuit](https://azure.microsoft.com/free/).

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>Afficher les mises à jour effectuées sur les identités managées affectées par l’utilisateur

Cette procédure montre comment afficher les mises à jour effectuées sur les identités managées affectées par l’utilisateur

1. Dans le portail Azure, accédez à **Journal d'activité**.

 ![Accéder au journal d’activité dans le portail Azure](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. Sélectionnez la recherche **Ajouter un filtre** et sélectionnez **Opération** dans la liste.

![Commencer à générer le filtre de recherche](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. Dans la liste déroulante **Opération**, entrez les noms d’opérations suivants : « Delete User Assigned Identity » et « Write UserAssignedIdentities ».

![Ajouter des opérations au filtre de recherche](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. Lorsque les opérations correspondantes sont affichées, sélectionnez-en une pour afficher le résumé.

![Afficher le résumé de l’opération](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. Sélectionnez l’onglet **JSON** pour obtenir des informations détaillées sur l’opération, et faites défiler jusqu’au nœud **properties** pour obtenir des informations sur l’identité qui a été modifiée.

![Afficher les détails de l’opération](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>Afficher les attributions de rôles ajoutées et supprimées pour les identités managées

 > [!NOTE] 
 > Vous devrez effectuer une recherche en fonction de l’ID d’objet (principal) de l’identité managée pour laquelle vous souhaitez afficher les modifications d’attribution de rôle.

1. Localisez l’identité managée pour laquelle vous souhaitez afficher les modifications d’attribution de rôle. Si vous recherchez une identité managée affectée par le système, l’ID d’objet s’affiche dans l’écran **Identité** sous la ressource. Si vous recherchez une identité affectée par l’utilisateur, l’ID d’objet s’affiche dans la page **Vue d’ensemble** de l’identité managée.

Identité affectée par l’utilisateur :

![Obtenir d’ID d’objet de l’identité affectée par l’utilisateur](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

Identité affectée par le système :

![Obtenir l’ID d’objet de l’identité affectée par le système](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. Copiez l’ID d’objet.
3. Accédez au **Journal d’activité**.

 ![Accéder au journal d’activité dans le portail Azure](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. Sélectionnez la recherche **Ajouter un filtre** et sélectionnez **Opération** dans la liste.

![Commencer à générer le filtre de recherche](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. Dans la liste déroulante **Opération**, entrez les noms d’opérations suivants : « Create role assignment » et « Delete role assignment ».

![Ajouter des opérations d’attribution de rôle au filtre de recherche](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. Collez l’ID d’objet dans la zone de recherche. Les résultats seront filtrés automatiquement.

![Rechercher par ID d’objet](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. Lorsque les opérations correspondantes sont affichées, sélectionnez-en une pour afficher le résumé.
 
![Résumé de l’attribution de rôle pour l’identité managée](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>Afficher les tentatives d’authentification par identités managées

1. Accédez à **Azure Active Directory**.

![Accéder à Active Directory](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  Sélectionnez **Journaux de connexion** dans la section **Surveillance**.

![Sélectionner les journaux de connexion](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. Sélectionnez l’onglet **Connexions d’identité managée**.

![connexion d’identité managée](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. Les événements de connexion seront désormais filtrés par identités managées.

![événements de connexion d’identité managée](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  Pour afficher l’application Enterprise de l’identité dans Azure Active Directory, sélectionnez la colonne « ID d’identité managée ».
6.  Pour afficher la ressource Azure ou l’identité managée affectée par l'utilisateur, recherchez par nom dans la barre de recherche du portail Azure.

## <a name="next-steps"></a>Étapes suivantes

* [Identités managées pour les ressources Azure](./overview.md)
* [Journal d’activité Azure](../../azure-monitor/essentials/activity-log.md)
* [Journal de connexions Azure Active Directory](../reports-monitoring/concept-sign-ins.md)