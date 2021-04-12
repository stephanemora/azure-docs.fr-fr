---
title: Regrouper des serveurs pour l’évaluation avec Azure Migrate | Microsoft Docs
description: Explique comment grouper des serveurs avant d’exécuter une évaluation avec le service Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780852"
---
# <a name="create-a-group-for-assessment"></a>Créer un groupe pour l’évaluation

Cet article décrit comment créer des groupes de serveurs à évaluer avec Azure Migrate : Découverte et évaluation.

[Azure Migrate](migrate-services-overview.md) vous aide à migrer vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure. Le hub fournit des outils Azure pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendant (ISV) tiers. 

## <a name="grouping-servers"></a>Regroupement de serveurs

Vous rassemblez les serveurs en groupes pour déterminer s’ils sont conviennent à une migration vers Azure et pour obtenir des estimations de coût et de dimensionnement Azure. Il existe deux façons de créer des groupes :

- Si vous connaissez les serveurs à migrer ensemble, vous pouvez créer manuellement le groupe dans Azure Migrate.
- Si vous n’êtes pas certain des serveurs à regrouper, vous pouvez utiliser la fonctionnalité de visualisation des dépendances dans Azure Migrate pour créer des groupes. 

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="create-a-group-manually"></a>Créer un groupe manuellement

Vous pouvez créer un groupe en même temps que vous [créez une évaluation](how-to-create-assessment.md).

Si vous souhaitez créer un groupe manuellement en dehors de la création d’une évaluation, procédez comme suit :

1. Dans le projet Azure Migrate > **Vue d’ensemble**, cliquez sur **Évaluer et migrer des serveurs**. Dans **Azure Migrate : Découverte et évaluation**, cliquez sur **Groupes**.
    - Si vous n’avez pas encore ajouté l’outil Azure Migrate : Découverte et évaluation, cliquez pour l’ajouter. [Plus d’informations](how-to-assess.md)
    - Si vous n’avez pas encore créé de projet Azure Migrate, [cliquez ici pour en savoir plus](./create-manage-projects.md).

    ![Sélection de groupes](./media/how-to-create-a-group/select-groups.png)

2. Cliquez sur l’icône **Groupe**.
3. Dans **Créer un groupe**, spécifiez un nom de groupe et, dans **Nom de l’appliance**, sélectionnez l’appliance Azure Migrate que vous utilisez pour la détection de serveurs.
4. Dans la liste de serveurs, sélectionnez les serveurs que vous souhaitez ajouter au groupe > **Créer**.

    ![Créer un groupe](./media/how-to-create-a-group/create-group.png)

Vous pouvez maintenant utiliser ce groupe lorsque vous [créez une évaluation de machine virtuelle Azure](how-to-create-assessment.md), [une évaluation Azure VMware Solution (AVS)](how-to-create-azure-vmware-solution-assessment.md) ou [une évaluation Azure SQL](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Affiner un groupe avec un mappage des dépendances

Le mappage des dépendances vous permet de visualiser les dépendances entre les serveurs. En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les groupes de serveurs avec un niveau de confiance plus élevé.
- Il vous aide à vérifier les dépendances des serveurs avant d’exécuter une évaluation. 
- Il vous aide également à planifier efficacement votre migration vers Azure, en vous assurant que rien n’est oublié et en évitant ainsi les interruptions surprises durant la migration.
- Vous pouvez découvrir les systèmes interdépendants qui doivent migrer en même temps et déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.

Si vous avez déjà [configuré le mappage des dépendances](how-to-create-group-machine-dependencies.md) et que vous souhaitez affiner un groupe existant, procédez comme suit :

1. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : Découverte et évaluation**, cliquez sur **Groupes**.
2. Cliquez sur le groupe que vous souhaitez affiner.
    - Si vous n’avez pas encore configuré le mappage des dépendances, la colonne **Dépendances** indique un état **Installation requise**. Pour chaque serveur pour lequel vous souhaitez visualiser les dépendances, cliquez sur **Installation requise**. Installez quelques agents sur chaque serveur avant de pouvoir mapper les dépendances du serveur. [Plus d’informations](how-to-create-group-machine-dependencies.md)

        ![Ajouter un mappage des dépendances](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Si vous avez déjà configuré le mappage des dépendances, dans la page groupe, cliquez sur **Afficher les dépendances** pour ouvrir le mappage des dépendances de groupe.

3. Après avoir cliqué sur **Afficher les dépendances**, la carte des dépendances de groupe affiche les éléments suivants :

    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers et depuis tous les serveurs du groupe sur lesquels sont installés les agents de dépendance.
    - Les serveurs dépendants sur lesquels les agents de dépendance ne sont pas installés sont regroupés par numéro de port.
    - Les serveurs dépendants sur lesquels des agents de dépendance sont installés sont affichés dans des zones distinctes.
    - Processus en cours d’exécution à l’intérieur du serveur. Développez la zone de chaque serveur pour afficher les processus.
    - Propriétés du serveur ( incluant le nom de domaine complet, le système d’exploitation, l’adresse MAC). Cliquez la zone de chaque serveur pour afficher les détails.

4. Pour afficher les dépendances dans un intervalle de temps de votre choix, modifiez l’intervalle de temps (par défaut, une heure) en spécifiant les dates de début et de fin, ou la durée.

    > [!NOTE]
    > L’intervalle de temps peut atteindre une heure. Si vous avez besoin d’un intervalle plus long, utilisez [Azure Monitor pour interroger les données dépendantes](how-to-create-group-machine-dependencies.md) pendant une période plus longue.

5. Une fois que vous avez identifié les dépendances que vous souhaitez ajouter ou supprimer dans le groupe, vous pouvez modifier le groupe. Utilisez Ctrl+clic pour ajouter ou supprimer des serveurs dans le groupe.

    - Seuls les serveurs qui ont été découverts peuvent être ajoutés.
    - L’ajout et la suppression de serveurs au sein d’un groupe invalide ses évaluations passées.
    - Vous pouvez, si vous le souhaitez, créer une nouvelle évaluation lorsque vous modifiez le groupe.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer et utiliser le [mappage des dépendances](how-to-create-group-machine-dependencies.md) pour créer des groupes de confiance élevée.