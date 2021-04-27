---
title: Supprimer un projet Azure Migrate
description: Cet article vous permettra d'apprendre à supprimer un projet Azure Migrate à l'aide du portail Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714737"
---
# <a name="delete-an-azure-migrate-project"></a>Supprimer un projet Azure Migrate

Cet article explique comment supprimer un projet [Azure Migrate](./migrate-services-overview.md).


## <a name="before-you-start"></a>Avant de commencer

Avant de supprimer un projet :

- Quand vous supprimez un projet, le projet ainsi que les métadonnées des machines détectées sont supprimés.
- Si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment pour l’analyse des dépendances, déterminez si vous voulez supprimer l’espace de travail. 
    - L’espace de travail n’est pas supprimé automatiquement. Supprimez-le manuellement.
    - Vérifiez la fonction d’un espace de travail avant de le supprimer. Le même espace de travail Log Analytics peut être utilisé dans plusieurs scénarios.
    - Avant de supprimer le projet, vous disposez d’un lien vers l’espace de travail dans **Azure Migrate - Serveurs** > **Azure Migrate - Server Assessment**, sous **Espace de travail OMS**.
    - Pour supprimer un espace de travail après la suppression d’un projet, recherchez l’espace de travail dans le groupe de ressources approprié, puis suivez [ces instructions](../azure-monitor/logs/delete-workspace.md).


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

### <a name="projects-with-public-endpoint-connectivity"></a>Projets avec connectivité de point de terminaison public

#### <a name="vmwarephysical-server"></a>VMware/serveur physique

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
migrateapplisbns* | Espace de noms Service Bus

#### <a name="hyper-v-vm"></a>Machine virtuelle Hyper-V

**Ressource** | **Type**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | Coffre de clés
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Coffre Recovery Services

<br/>
Les tableaux suivants résument les ressources créées par Azure Migrate pour découvrir, évaluer et migrer des serveurs sur un réseau privé à l’aide d’une [liaison privée Azure](./how-to-use-azure-migrate-with-private-endpoints.md).

### <a name="projects-with-private-endpoint-connectivity"></a>Projets avec connectivité de point de terminaison privé

#### <a name="vmware-vms---agentless-migrations"></a>Migrations sans agent - Machines virtuelles VMware

**Type** | **Ressource** | **Point de terminaison privé <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
Site de découverte (site maître) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Coffre de clés | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | N/D
Coffre Recovery Services | "ApplianceName"*vault | N/D
Compte de stockage | "ApplianceName"*usa | "ApplianceName"\*usa\*pe
Coffre Recovery Services | "ProjectName"-MigrateVault-* | N/D
Compte de stockage | migrateappligwsa* | N/D
Compte de stockage | migrateapplilsa* | N/D
Coffre de clés | migrateapplikv* | N/D
Espace de noms Service Bus | migrateapplisbns* | N/D

#### <a name="hyper-v-vms"></a>Machines virtuelles Hyper-V 

**Type** | **Ressource** | **Point de terminaison privé <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
Site de découverte (site maître) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Coffre de clés | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | N/D
Coffre Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Serveurs physiques/machines virtuelles AWS/machines virtuelles GCP 

**Type** | **Ressource** | **Point de terminaison privé <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
Site de découverte (site maître) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Coffre de clés | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | N/D
Coffre Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des outils d’[évaluation](how-to-assess.md) et de [migration](how-to-migrate.md) supplémentaires. 
