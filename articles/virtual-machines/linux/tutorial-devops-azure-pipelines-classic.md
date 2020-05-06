---
title: Tutoriel – Configurer des déploiements Rolling pour les machines virtuelles Linux Azure
description: Dans ce tutoriel, vous allez découvrir comment configurer un pipeline de déploiement continu (CD) qui met à jour de façon incrémentielle un groupe de machines virtuelles Linux Azure en utilisant une stratégie de déploiement Rolling
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113479"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutoriel – Configurer une stratégie de déploiement Rolling pour des machines virtuelles Linux Azure

Azure DevOps est un service Azure intégré qui automatise chaque partie du processus DevOps à l’aide de l’intégration continue et de la livraison continue pour toutes les ressources Azure.
Que votre application utilise des machines virtuelles, des applications web, Kubernetes ou toute autre ressource, vous pouvez implémenter une infrastructure sous forme de code, une intégration continue, des tests continus, une livraison continue et une supervision continue avec Azure et Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - Configurer les outils CI/CD 
Azure Pipelines fournit un ensemble complet d’outils d’automatisation CI/CD pour les déploiements sur des machines virtuelles. Vous pouvez configurer un pipeline de livraison continue pour une machine virtuelle Azure directement à partir du portail Azure. Ce document contient les étapes associées à la configuration d’un pipeline CI/CD pour les déploiements multimachines Rolling à partir du portail Azure. Vous pouvez aussi vous pencher sur d’autres stratégies comme [canary](https://aka.ms/AA7jdrz) et [blue-green](https://aka.ms/AA83fwu), qui sont prises en charge en l’état sur le portail Azure. 


**Configurer les outils CI/CD sur les machines virtuelles**

Les machines virtuelles peuvent être ajoutées en tant que cibles à un [groupe de déploiement](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups), et peuvent être ciblées pour des mises à jour propagées sur plusieurs machines. Une fois le déploiement effectué, l’**historique de déploiement** d’un groupe de déploiement offre une traçabilité entre la machine virtuelle et le pipeline, puis entre le pipeline et la validation. 
 

**Déploiements propagés (Rolling)**  : Un déploiement propagé remplace les instances de la version précédente d’une application par les instances de la nouvelle version de l’application sur un ensemble fixe de machines (ensemble propagé) à chaque itération. Voyons comment configurer une mise à jour propagée sur des machines virtuelles.  
Vous pouvez configurer des mises à jour propagées sur vos « **machines virtuelles** » dans le portail Azure à l’aide de l’option de livraison continue. 

Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle. 
2. Dans le volet gauche de la machine virtuelle, accédez au menu **Livraison continue** . Cliquez ensuite sur **Configurer**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Dans le volet de configuration, cliquez sur « Organisation Azure DevOps » pour sélectionner un compte ou en créer un. Sélectionnez ensuite le projet dans lequel vous souhaitez configurer le pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un groupe de déploiement est un ensemble logique de machines cibles de déploiement qui représentent les environnements physiques, par exemple « Dev », « Test », « UAT » et « Production ». Vous pouvez créer un groupe de déploiement, ou en sélectionner un qui existe déjà. 
5. Sélectionnez le pipeline de build qui publie le package à déployer sur la machine virtuelle. Notez que le script de déploiement _deploy.ps1_ ou _deploy.sh_ doit se trouver dans le dossier `deployscripts` à la racine du package publié. Ce script de déploiement sera exécuté par le pipeline Azure DevOps au moment de l’exécution.
6. Sélectionnez la stratégie de déploiement de votre choix. Dans ce cas, nous allons sélectionner « Rolling ».
7. Si vous le souhaitez, vous pouvez étiqueter la machine en fonction du rôle. Exemple : « web », « base de données », etc. Cela vous permet de cibler uniquement les machines virtuelles qui ont un rôle spécifique.
8. Cliquez sur **OK** dans la boîte de dialogue pour configurer le pipeline de livraison continue. 
9. Une fois l’opération effectuée, vous disposez d’un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Vous voyez que le déploiement sur la machine virtuelle est en cours. Vous pouvez cliquer sur le lien pour accéder au pipeline. Cliquez sur **Release-1** pour voir le déploiement. Vous pouvez également cliquer sur **Modifier** pour modifier la définition du pipeline de mise en production. 
11. Si vous avez plusieurs machines virtuelles à configurer, répétez les étapes 2 à 4 pour les autres machines virtuelles à ajouter au groupe de déploiement. Notez que si vous sélectionnez un groupe de déploiement pour lequel une exécution de pipeline existe déjà, la machine virtuelle est simplement ajoutée au groupe de déploiement, sans entraîner la création de nouveaux pipelines. 
12. Une fois l’opération effectuée, cliquez sur la définition du pipeline, accédez à l’organisation Azure DevOps, puis cliquez sur **Modifier** pour modifier le pipeline de mise en production. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Cliquez sur le lien **1 job, 1 task** (1 travail, 1 tâche) à la phase **dev**. Cliquez sur la phase **Déployer**.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. Dans le volet de configuration de droite, vous pouvez spécifier le nombre de machines que vous souhaitez déployer en parallèle dans chaque itération. Si vous souhaitez effectuer un déploiement sur plusieurs machines à la fois, vous pouvez le spécifier à l’aide d’un pourcentage avec le curseur.  

15. Par défaut, la tâche Execute Deploy Script (Exécuter le script de déploiement) exécute le script de déploiement _deploy.ps1_ ou _deploy.sh_ qui se trouve dans le dossier « deployscripts » situé dans le répertoire racine du package publié.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Autres stratégies de déploiement

- [Configurer une stratégie de déploiement Canary](https://aka.ms/AA7jdrz)
- [Configurer une stratégie de déploiement Blue-Green](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps Projects 
Découvrez comment bien démarrer avec Azure plus facilement que jamais.
 
Avec DevOps Projects, commencez à exécuter votre application sur n’importe quel service Azure en seulement trois étapes : sélectionnez un langage d’application, un runtime et un service Azure.
 
[Plus d’informations](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Ressources supplémentaires 
- [Déployer sur des machines virtuelles Azure à l’aide de DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implémenter un déploiement continu de votre application sur un groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
