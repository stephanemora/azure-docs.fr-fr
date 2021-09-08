---
title: Guide pratique pour ajouter des propriétaires à un labo dans Azure Lab Services
description: Cet article explique comment un administrateur peut ajouter un utilisateur à un labo comme propriétaire dans Azure Lab Services.
ms.topic: article
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: e8dd21a140e1c519f09d661c776e5c84c47da925
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635010"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Guide pratique pour ajouter des propriétaires à un labo existant dans Azure Lab Services
Cet article vous explique comment, en tant qu’administrateur, vous pouvez ajouter des propriétaires à un labo existant.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Ajouter un utilisateur au rôle de lecteur pour le compte Lab
1. De retour dans la page **Compte Lab**, sélectionnez **Tous les labs** dans le menu de gauche.
2. Sélectionnez le **labo** auquel vous voulez ajouter l’utilisateur en tant que propriétaire. 

    ![Sélectionner le laboratoire ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. Dans le menu de navigation, cliquez sur **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

    ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Dans l’onglet **Rôle**, sélectionnez le rôle **lecteur**.

    ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Dans l’onglet **Membres**, sélectionnez l’utilisateur auquel vous souhaitez ajouter le rôle Lecteur.

1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.
## <a name="add-user-to-the-owner-role-for-the-lab"></a>Ajouter un utilisateur au rôle de lecteur pour le labo

> [!NOTE]
> Si l’utilisateur dispose d’un accès en lecture seule sur un labo, ce dernier ne s’affiche pas dans labs.azure.com. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).


1. Sur la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)**

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

    ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Dans l’onglet **Rôle**, sélectionnez le rôle **Propriétaire**.

    ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Dans l’onglet **Membres**, sélectionnez l’utilisateur auquel vous souhaitez ajouter le rôle Propriétaire.

1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.


## <a name="next-steps"></a>Étapes suivantes
Vérifiez que l’utilisateur voit le labo quand il se connecte au [portail Lab Services](https://labs.azure.com).
