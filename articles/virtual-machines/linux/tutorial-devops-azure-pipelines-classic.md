---
title: Tutoriel - DevOps intégré pour IaaS et PaaS sur Azure
description: Dans ce tutoriel, vous allez apprendre à configurer l’intégration continue (CI) et le déploiement continu (CD) d’une application sur des machines virtuelles Azure à l’aide d’Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: eba793a761bd9f96b1a4ec5d4730f08187a758ef
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515256"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutoriel : DevOps intégré pour IaaS et PaaS sur Azure

Avec des solutions de bout en bout sur Azure, les équipes peuvent implémenter des pratiques DevOps dans chacune des phases du cycle de vie des applications : planification, développement, livraison et fonctionnement. 

Vous trouverez ci-dessous quelques-uns des services Azure qui simplifient les charges de travail cloud, et que vous pouvez combiner dans le cadre de scénarios étonnamment puissants.
Ces technologies, associées aux personnes et aux processus appropriés, permettent aux équipes d’apporter sans cesse de la valeur ajoutée aux clients. 

- Azure : https://portal.azure.com - Portail pour la création de charges de travail cloud. Gérez et supervisez tout, de simples applications web aux applications cloud complexes 
- Azure DevOps : https://dev.azure.com - Planifiez plus intelligemment, collaborez mieux et livrez plus rapidement à l’aide d’un ensemble de services de développement modernes 
- Azure Machine Learning Studio : https://ml.azure.com - Préparez les données, puis entraînez et déployez les modèles Machine Learning 
 

Azure DevOps est un service Azure intégré qui automatise chaque partie du processus DevOps à l’aide de l’intégration continue et de la livraison continue pour toutes les ressources Azure.
Que votre application utilise des machines virtuelles, des applications web, Kubernetes ou toute autre ressource, vous pouvez implémenter l’infrastructure sous forme de code, d’intégration continue, de tests continus, de livraison continue et de supervision continue avec Azure et Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configurer les outils CI/CD 
Azure Pipelines fournit un ensemble complet d’outils d’automatisation CI/CD pour les déploiements sur des machines virtuelles. Vous pouvez configurer un pipeline de livraison continue pour une machine virtuelle Azure directement à partir du portail Azure. Ce document contient les étapes associées à la configuration d’un pipeline CI/CD pour les déploiements multimachines à partir du portail Azure. Configurez les outils CI/CD sur les machines virtuelles.

Les machines virtuelles peuvent être ajoutées en tant que cibles à un [groupe de déploiement](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups), et peuvent être ciblées pour des mises à jour propagées sur plusieurs machines. Les vues de l’historique de déploiement dans les groupes de déploiement offrent une traçabilité de la machine virtuelle au pipeline, puis à la validation. 
 
**Mises à jour propagées** : Un déploiement propagé remplace les instances de la version précédente d’une application par les instances de la nouvelle version de l’application sur un ensemble fixe de machines (ensemble propagé) à chaque itération. Voyons comment configurer une mise à jour propagée sur des machines virtuelles.  
Vous pouvez configurer des mises à jour propagées sur vos « **machines virtuelles** » dans le portail Azure à l’aide de l’option de livraison continue. 

Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle. 
2. Dans le volet gauche de la machine virtuelle, accédez au menu **Livraison continue** . Cliquez ensuite sur **Configurer**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. Dans le volet de configuration, cliquez sur « Organisation Azure DevOps » pour sélectionner un compte ou en créer un. Sélectionnez ensuite le projet dans lequel vous souhaitez configurer le pipeline.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Un groupe de déploiement est un ensemble logique de machines cibles de déploiement qui représentent les environnements physiques, par exemple « Dev », « Test », « UAT » et « Production ». Vous pouvez créer un groupe de déploiement, ou en sélectionner un qui existe déjà. Vous pouvez éventuellement étiqueter la machine en fonction du rôle. Exemple : « web », « base de données », etc.  
5. Cliquez sur **OK** dans la boîte de dialogue pour configurer le pipeline de livraison continue. 
6. Une fois l’opération effectuée, vous disposez d’un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Vous voyez que le déploiement sur la machine virtuelle est en cours. Vous pouvez cliquer sur le lien pour accéder au pipeline. Cliquez sur **Release-1** pour voir le déploiement. Vous pouvez également cliquer sur **Modifier** pour modifier la définition du pipeline de mise en production. 
8. Si vous avez plusieurs machines virtuelles à configurer, répétez les étapes 2 à 5 pour les autres machines virtuelles à ajouter au groupe de déploiement. 
9. Une fois l’opération effectuée, cliquez sur la définition du pipeline, accédez à l’organisation Azure DevOps, puis cliquez sur **Modifier** pour modifier le pipeline de mise en production. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Cliquez sur le lien **1 job, 1 task** (1 travail, 1 tâche) à la phase **dev**. Cliquez sur la phase **Déployer**.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. Dans le volet de configuration à droite, vous pouvez voir que, par défaut, le pipeline est configuré pour effectuer une mise à jour propagée sur toutes les cibles en parallèle. Vous pouvez configurer les déploiements pour qu’ils s’effectuent l’un après l’autre, ou en fonction de pourcentages à l’aide du curseur.  
  
  
**Canari** est un type de déploiement qui réduit les risques, car les changements sont déployés lentement sur un petit sous-ensemble d’utilisateurs. À mesure que se renforce votre confiance en la nouvelle version, vous pouvez commencer à la mettre en production sur davantage de serveurs de votre infrastructure et y router plus d’utilisateurs. Vous pouvez configurer des déploiements canari sur vos « **machines virtuelles** » via le portail Azure à l’aide de l’option de livraison continue. Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Suivez les étapes 2 à 5 de la section précédente pour ajouter plusieurs machines virtuelles au groupe de déploiement. 
3. Ajoutez une étiquette personnalisée aux machines virtuelles qui doivent faire partie de déploiements canari. Exemple : « canari ».
4. Une fois le pipeline configuré pour les machines virtuelles, cliquez sur celui-ci, lancez l’organisation Azure DevOps, **Modifiez** le pipeline, puis accédez à l’étape **dev**. Ajoutez l’ étiquette au filtre « canari ». 
5. Ajoutez une autre phase de groupe de déploiement, configurez-la à l’aide des étiquettes pour cibler les machines virtuelles restantes dans le groupe de déploiement.  
6. Vous pouvez éventuellement configurer une étape de validation manuelle pour promouvoir/rejeter les déploiements canari. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Bleu/vert** est un type de déploiement qui permet de réduire les temps d’arrêt à l’aide d’un environnement de secours identique. À tout moment, l’un des environnements est actif. Pendant la préparation d’une nouvelle mise en production, vous effectuez votre dernière étape de tests dans l’environnement vert. Une fois que le logiciel fonctionne dans l’environnement vert, changez le trafic pour router toutes les requêtes entrantes en direction de l’environnement vert, l’environnement bleu étant désormais inactif.
Vous pouvez configurer des déploiements bleu/vert sur vos « **machines virtuelles** » à partir du portail Azure via l’option de livraison continue. 

Voici la procédure pas à pas. 

1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Suivez les étapes 2 à 5 de la section **Mises à jour propagées** pour ajouter plusieurs machines virtuelles au groupe de déploiement. Ajoutez une étiquette personnalisée aux machines virtuelles qui doivent faire partie des déploiements bleu/vert. Par exemple, « bleu » ou « vert » pour les machines virtuelles ayant un rôle de secours. 
3. Une fois le pipeline configuré pour les machines virtuelles, cliquez sur celui-ci, lancez l’organisation Azure DevOps, **Modifiez** le pipeline, puis accédez à l’étape **dev**. Ajoutez l’étiquette au filtre « vert ». 
4. Ajoutez une phase sans agent, configurez la phase avec une étape de validation manuelle et une étape d’appel d’API REST pour échanger les étiquettes. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps Projects 
Découvrez comment bien démarrer avec Azure plus facilement que jamais.
 
Avec DevOps Projects, commencez à exécuter votre application sur n’importe quel service Azure en seulement trois étapes : sélectionnez un langage d’application, un runtime et un service Azure.
 
[Plus d’informations](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Ressources supplémentaires 
- [Déployer sur des machines virtuelles Azure à l’aide de DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implémenter un déploiement continu de votre application sur un groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
