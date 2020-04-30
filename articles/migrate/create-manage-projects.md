---
title: Créer et gérer des projets Azure Migrate
description: Rechercher, créer, gérer et supprimer des projets dans Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676391"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Créer et gérer des projets Azure Migrate

Cet article explique comment créer, gérer et supprimer des projets [Azure Migrate](migrate-services-overview.md).


## <a name="create-a-project-for-the-first-time"></a>Créer un projet pour la première fois

La première fois que vous configurez Azure Migrate, vous créez un projet et ajoutez un outil d’évaluation ou de migration. [Suivez ces instructions](how-to-add-tool-first-time.md) lors de la première configuration.

## <a name="create-additional-projects"></a>Créer des projets supplémentaires

Si vous disposez déjà d’un projet Azure Migrate et que vous souhaitez créer un projet supplémentaire, procédez comme suit :  

1. Dans le [portail public Azure](https://portal.azure.com) ou [Azure Government](https://portal.azure.us), recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

   ![Modifier un projet Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Pour créer un nouveau projet, sélectionnez **cliquez ici**.

   ![Créer un deuxième projet Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Rechercher un projet

Recherchez un projet comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

    ![Passer à un projet Azure Migrate existant](./media/create-manage-projects/switch-project.png)

3. Sélectionnez l’abonnement et le projet Azure Migrate de votre choix.


Si vous avez créé le projet dans la [version précédente](migrate-services-overview.md#azure-migrate-versions) d’Azure Migrate, recherchez-le comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate, si vous avez créé un projet dans la version précédente, une bannière faisant référence aux projets plus anciens s’affiche. Sélectionnez la bannière.

    ![Accéder aux projets existants](./media/create-manage-projects/access-existing-projects.png)

3. Passez en revue la liste des anciens projets.


## <a name="delete-a-project"></a>Supprimer un projet

Supprimez comme suit :

1. Ouvrez le groupe de ressources Azure dans lequel le projet a été créé.
2. Dans la page du groupe de ressources, sélectionnez **Afficher les types masqués**.
3. Sélectionnez le projet de migration à supprimer, ainsi que les ressources qui lui sont associées.
    - Le type de ressource est **Microsoft.Migrate/migrateprojects**.
    - Si le groupe de ressources est utilisé uniquement par le projet Azure Migrate, vous pouvez supprimer l’intégralité du groupe de ressources.


Notez les points suivants :

- Quand vous supprimez, le projet et les métadonnées concernant les machines découvertes sont supprimés.
- Si vous utilisez l’ancienne version d’Azure Migrate, ouvrez le groupe de ressources Azure dans lequel le projet a été créé. Sélectionnez le projet de migration à supprimer (le type de ressource est **Migration project**).
- Si vous utilisez l’analyse des dépendances avec un espace de travail Azure Log Analytics :
    - Cependant, si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment, l’espace de travail n’est pas supprimé automatiquement. Le même espace de travail Log Analytics peut être utilisé dans plusieurs scénarios.
    - Si vous souhaitez supprimer l’espace de travail Log Analytics, faites-le manuellement.

### <a name="delete-a-workspace-manually"></a>Supprimer un espace de travail manuellement

1. Accédez à l’espace de travail Log Analytics associé au projet.

    - Si vous n’avez pas supprimé le projet Azure Migrate, vous trouverez le lien vers l’espace de travail dans **Essentials** > **Server Assessment**.
       ![Espace de travail LA](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Si vous avez déjà supprimé le projet Azure Migrate, sélectionnez **Groupes de ressources** dans le volet gauche du Portail Azure et recherchez l’espace de travail.
       
2. [Suivez les instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) pour supprimer l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Ajoutez des outils d’[évaluation](how-to-assess.md) ou de [migration](how-to-migrate.md) à des projets Azure Migrate.
