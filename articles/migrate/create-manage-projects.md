---
title: Créer et gérer des projets
description: Rechercher, créer, gérer et supprimer des projets dans Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871068"
---
# <a name="create-and-manage-projects"></a>Créer et gérer des projets

Cet article explique comment créer, gérer et supprimer des [projets](migrate-services-overview.md). 

Azure Migrate classique sera mis hors service en février 2024. Après février 2024, la version classique d’Azure Migrate ne sera plus prise en charge et les métadonnées d’inventaire dans le projet classique seront supprimées. Si vous utilisez des projets classiques, supprimez-les et suivez la procédure pour créer un projet. Il n’est pas possible de mettre à niveau des projets ni des composants classiques vers Azure Migrate. Affichez la [FAQ](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) avant de commencer le processus de création.

Un projet est utilisé pour stocker les métadonnées de découverte, d’évaluation et de migration collectées à partir de l’environnement que vous évaluez ou migrez. Dans un projet, vous pouvez suivre les ressources découvertes, créer des évaluations et orchestrer des migrations vers Azure.  

## <a name="verify-permissions"></a>Vérification des autorisations

Vérifiez que vous disposez des autorisations correctes pour créer un projet :

1. Dans le portail Azure, ouvrez l’abonnement approprié, puis sélectionnez  **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis sélectionnez-le pour afficher les autorisations. Vous devez disposer des autorisations de *Contributeur* ou de *Propriétaire*. 


## <a name="create-a-project-for-the-first-time"></a>Créer un projet pour la première fois

Configurez un nouveau projet dans un abonnement Azure.

1. Dans le portail Azure, recherchez *Azure Migrate*.
2. Dans **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sélectionnez **Évaluer et migrer des serveurs**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Option dans Vue d’ensemble pour évaluer et migrer des serveurs":::

4. Dans **Serveurs**, sélectionnez **Créer un projet**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Bouton permettant de démarrer la création d’un projet":::

5. Dans **Créer un projet**, sélectionnez l’abonnement et le groupe de ressources Azure. Créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer.
    - La zone géographique est utilisée uniquement pour stocker les métadonnées collectées à partir de serveurs locaux. Vous pouvez sélectionner n’importe quelle région cible pour la migration. 
    - Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

8. Sélectionnez **Create** (Créer).

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Page dans laquelle entrer les paramètres du projet":::


Attendez quelques minutes, le temps nécessaire au déploiement du projet.

## <a name="create-a-project-in-a-specific-region"></a>Créer un projet dans une région spécifique

Dans le portail, vous pouvez sélectionner la zone géographique dans laquelle vous voulez créer le projet. Si vous voulez créer le projet dans une région Azure spécifique, utilisez la commande API suivante pour créer le projet.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Créer des projets supplémentaires

Si vous disposez déjà d’un projet et que vous souhaitez créer un projet supplémentaire, procédez comme suit :  

1. Dans le [portail public Azure](https://portal.azure.com) ou [Azure Government](https://portal.azure.us), recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Changer le projet":::

3. Pour créer un nouveau projet, sélectionnez **cliquez ici**.


## <a name="find-a-project"></a>Rechercher un projet

Recherchez un projet comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez *Azure Migrate*.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Basculer vers un projet existant":::

3. Sélectionnez l’abonnement et le projet appropriés.


### <a name="find-a-classic-project"></a>Rechercher un projet classique

Si vous avez créé le projet dans la [version précédente](migrate-services-overview.md#azure-migrate-versions) d’Azure Migrate, recherchez-le comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez *Azure Migrate*.
2. Dans le tableau de bord Azure Migrate, si vous avez créé un projet dans la version précédente, une bannière faisant référence aux projets plus anciens s’affiche. Sélectionnez la bannière.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Accéder aux projets existants":::

3. Passez en revue la liste des anciens projets.


## <a name="delete-a-project"></a>Supprimer un projet

Supprimez comme suit :

1. Ouvrez le groupe de ressources Azure dans lequel le projet a été créé.
2. Dans la page du groupe de ressources, sélectionnez **Afficher les types masqués**.
3. Sélectionnez le projet à supprimer, ainsi que les ressources qui lui sont associées.
    - Le type de ressource est **Microsoft.Migrate/migrateprojects**.
    - Si le groupe de ressources est utilisé uniquement par le projet, vous pouvez supprimer l’intégralité du groupe de ressources.

Notez les points suivants :

- Lorsque vous supprimez, le projet et les métadonnées concernant les serveurs découverts sont supprimés.
- Si vous utilisez l’ancienne version d’Azure Migrate, ouvrez le groupe de ressources Azure dans lequel le projet a été créé. Sélectionnez le projet à supprimer (le type de ressource est **Migration project**).
- Si vous utilisez l’analyse des dépendances avec un espace de travail Azure Log Analytics :
    - Cependant, si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment, l’espace de travail n’est pas supprimé automatiquement. Le même espace de travail Log Analytics peut être utilisé dans plusieurs scénarios.
    - Si vous souhaitez supprimer l’espace de travail Log Analytics, faites-le manuellement.
- La suppression du projet est irréversible. Les objets supprimés ne peuvent pas être récupérés.

### <a name="delete-a-workspace-manually"></a>Supprimer un espace de travail manuellement

1. Accédez à l’espace de travail Log Analytics associé au projet.

    - Si vous n’avez pas supprimé le projet, vous trouverez le lien vers l’espace de travail dans **Essentials** > **Server Assessment**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="Espace de travail Log Analytics":::
       
    - Si vous avez déjà supprimé le projet, sélectionnez **Groupes de ressources** dans le volet gauche du Portail Azure et recherchez l’espace de travail.
       
2. [Suivez les instructions](../azure-monitor/logs/delete-workspace.md) pour supprimer l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Ajoutez des outils d’[évaluation](how-to-assess.md) ou de [migration](how-to-migrate.md) à des projets.