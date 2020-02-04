---
title: Intégration et déploiement continus
description: Expérience DevOps de base de données de classe entreprise pour SQL Data Warehouse avec prise en charge intégrée de l’intégration et du déploiement continus à l’aide d’Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a8178e5ff9ff4816ddd422d3c45cfc0e1e0b3d41
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712989"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Intégration et déploiement continus pour Azure SQL Data Warehouse

Ce tutoriel simple décrit comment intégrer votre projet de base de données SQL Server Data Tools (SSDT) à Azure DevOps et comment tirer parti d’Azure Pipelines pour configurer l’intégration et le déploiement continus. Ce tutoriel constitue la seconde étape de la création de votre pipeline d’intégration et de déploiement continus avec SQL Data Warehouse. 

## <a name="before-you-begin"></a>Avant de commencer

- Parcourez le [tutoriel sur l’intégration du contrôle de code source](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Configurer Azure DevOps et s’y connecter


## <a name="continuous-integration-with-visual-studio-build"></a>Intégration continue avec la build Visual Studio

1. Accédez à Azure Pipelines et créez un pipeline de build.

      ![Nouveau pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nouveau pipeline")

2. Sélectionnez votre référentiel de code source (Azure Repos Git) et sélectionnez le modèle d’application de bureau .NET.

      ![Configuration du pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configuration du pipeline") 

3. Modifiez votre fichier YAML pour utiliser le pool approprié de votre agent. Votre fichier YAML doit ressembler à ce qui suit :

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

À ce stade, vous disposez d’un environnement simple dans lequel tout enregistrement dans la branche principale de votre référentiel de contrôle de code source doit déclencher automatiquement une build Visual Studio réussie de votre projet de base de données. Vérifiez que l’automatisation fonctionne de bout en bout en apportant une modification dans votre projet de base de données locale et en enregistrant cette modification dans votre branche principale.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Déploiement continu avec la tâche de déploiement Azure SQL Data Warehouse (ou Database)

1. Ajoutez une nouvelle tâche à l’aide de la [tâche de déploiement Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) et renseignez les champs obligatoires pour vous connecter à votre entrepôt de données cible. Pendant l’exécution de cette tâche, le DACPAC généré à partir du processus de génération précédent est déployé dans l’entrepôt de données cible. Vous pouvez également utiliser la [tâche de déploiement Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Tâche de déploiement](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Tâche de déploiement")

2. Si vous utilisez un agent autohébergé, veillez à définir votre variable d’environnement pour utiliser le bon fichier SqlPackage.exe pour SQL Data Warehouse. Le chemin doit être similaire à celui-ci :

      ![Variable d’environnement](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variable d’environnement")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Exécutez et validez votre pipeline. Vous pouvez apporter des modifications localement et les enregistrer dans votre contrôle de code source qui devraient générer une build et un déploiement automatiques.

## <a name="next-steps"></a>Étapes suivantes

- Explorer la section [Architecture Azure SQL Data Warehouse](massively-parallel-processing-mpp-architecture.md)
- [Créer rapidement un entrepôt de données SQL Data Warehouse](create-data-warehouse-portal.md)
- [Charger des exemples de données](sql-data-warehouse-load-sample-databases.md)
- Explorer les [vidéos](/azure/sql-data-warehouse/sql-data-warehouse-videos)
