---
title: Supprimer un projet Azure Migrate
description: Décrit comment créer un projet Azure Migrate et ajouter un outil d’évaluation et de migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510250"
---
# <a name="delete-an-azure-migrate-project"></a>Supprimer un projet Azure Migrate

Cet article explique comment supprimer un projet [Azure Migrate](migrate-overview.md).


## <a name="before-you-start"></a>Avant de commencer

Avant de supprimer un projet :

- Quand vous supprimez un projet, le projet ainsi que les métadonnées des machines détectées sont supprimés.
- Si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment pour l’analyse des dépendances, déterminez si vous voulez supprimer l’espace de travail. 
    - L’espace de travail n’est pas supprimé automatiquement. Supprimez-le manuellement.
    - Vérifiez la fonction d’un espace de travail avant de le supprimer. Le même espace de travail Log Analytics peut être utilisé dans plusieurs scénarios.
    - Avant de supprimer le projet, vous disposez d’un lien vers l’espace de travail dans **Azure Migrate - Serveurs** > **Azure Migrate - Server Assessment**, sous **Espace de travail OMS**.
    - Pour supprimer un espace de travail après la suppression d’un projet, recherchez l’espace de travail dans le groupe de ressources approprié, puis suivez [ces instructions](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Supprimer un projet


1. Dans le portail Azure, ouvrez le groupe de ressources dans lequel le projet a été créé.
2. Dans la page du groupe de ressources, sélectionnez **Afficher les types masqués**.
3. Sélectionnez le projet et les ressources associées que vous voulez supprimer.
    - Le type de ressource pour les projets Azure Migrate est **Microsoft.Migrate/migrateprojects**.
    - Dans la section suivante, passez en revue les ressources créées pour la découverte, l’évaluation et la migration dans un projet Azure Migrate.
    - Si le groupe de ressources contient uniquement le projet Azure Migrate, vous pouvez supprimer l’intégralité du groupe de ressources.
    - Si vous voulez supprimer un projet de la version précédente d’Azure Migrate, les étapes à effectuer sont les mêmes. Le type de ressource pour ces projets est **Projet de migration**.


## <a name="created-resources"></a>Ressources créées

Les tableaux suivants récapitulent les ressources créées pour la découverte, l’évaluation et la migration dans un projet Azure Migrate.

> [!NOTE]
> Supprimez le coffre de clés avec précaution car il est susceptible de contenir des clés de sécurité.

### <a name="vmwarephysical-server"></a>VMware/serveur physique

**Ressource** | **Type**
--- | ---
"Appliancename"kv | Coffre de clés
"Appliancename"site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Coffre Recovery Services
"ProjectName"-MigrateVault-* | Coffre Recovery Services
migrateappligwsa* | Compte de stockage
migrateapplilsa* | Compte de stockage
migrateapplicsa* | Compte de stockage
migrateapplikv* | Coffre de clés
migrateapplisbns16041 | Espace de noms Service Bus

### <a name="hyper-v-vm"></a>Machine virtuelle Hyper-V 

**Ressource** | **Type**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | Coffre de clés
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Coffre Recovery Services


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des outils d’[évaluation](how-to-assess.md) et de [migration](how-to-migrate.md) supplémentaires. 
