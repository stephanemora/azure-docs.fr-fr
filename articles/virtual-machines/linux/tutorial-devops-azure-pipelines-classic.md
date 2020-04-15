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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885873"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutoriel : DevOps intégré pour IaaS et PaaS sur Azure

Avec des solutions de bout en bout sur Azure, les équipes peuvent implémenter des pratiques DevOps dans chacune des phases du cycle de vie des applications : planification, développement, livraison et exploitation. 

Vous trouverez ci-dessous quelques-uns des services Azure qui simplifient les charges de travail cloud, et que vous pouvez combiner dans le cadre de scénarios étonnamment puissants.
Ces technologies, associées aux personnes et aux processus appropriés, permettent aux équipes d’apporter sans cesse de la valeur ajoutée aux clients. 

- Azure : https://portal.azure.com - Portail pour la création de charges de travail cloud. Gérez et supervisez tout, de simples applications web aux applications cloud complexes 
- Azure DevOps : https://dev.azure.com - Planifiez plus intelligemment, collaborez mieux et livrez plus rapidement à l’aide d’un ensemble de services de développement modernes 
- Azure Machine Learning Studio : https://ml.azure.com - Préparez les données, puis entraînez et déployez les modèles Machine Learning 
 

Azure DevOps est un service Azure intégré qui automatise chaque partie du processus DevOps à l’aide de l’intégration continue et de la livraison continue pour toutes les ressources Azure.
Que votre application utilise des machines virtuelles, des applications web, Kubernetes ou toute autre ressource, vous pouvez implémenter l’infrastructure sous forme de code, d’intégration continue, de tests continus, de livraison continue et de supervision continue avec Azure et Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configurer les outils CI/CD 
Azure Pipelines fournit un ensemble complet d’outils d’automatisation CI/CD pour les déploiements sur des machines virtuelles. Vous pouvez configurer un pipeline de livraison continue pour une machine virtuelle Azure directement à partir du portail Azure. Ce document contient les étapes associées à la configuration d’un pipeline CI/CD pour les déploiements multimachines à partir du portail Azure. 


**Configurer les outils CI/CD sur les machines virtuelles**

Les machines virtuelles peuvent être ajoutées en tant que cibles à un [groupe de déploiement](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups), et peuvent être ciblées pour des mises à jour propagées sur plusieurs machines. En fonction de vos besoins, vous pouvez choisir n’importe quelle stratégie de déploiement prête à l’emploi (_Rolling_, _Canary_, _Blue-Green_) et même la personnaliser. Une fois le déploiement effectué, les vues de l’historique de déploiement dans les groupes de déploiement offrent une traçabilité entre la machine virtuelle et le pipeline, puis entre le pipeline et la validation. 
 
**Déploiements propagés (Rolling)**  : Un déploiement propagé remplace les instances de la version précédente d’une application par les instances de la nouvelle version de l’application sur un ensemble fixe de machines (ensemble propagé) à chaque itération. Voyons comment configurer une mise à jour propagée sur des machines virtuelles.  
Vous pouvez configurer des mises à jour propagées sur vos « **machines virtuelles** » dans le portail Azure à l’aide de l’option de livraison continue. 

Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle. 
2. Dans le volet gauche de la machine virtuelle, accédez au menu **Livraison continue** . Cliquez ensuite sur **Configurer**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Dans le volet de configuration, cliquez sur « Organisation Azure DevOps » pour sélectionner un compte ou en créer un. Sélectionnez ensuite le projet dans lequel vous souhaitez configurer le pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un groupe de déploiement est un ensemble logique de machines cibles de déploiement qui représentent les environnements physiques, par exemple « Dev », « Test », « UAT » et « Production ». Vous pouvez créer un groupe de déploiement, ou en sélectionner un qui existe déjà. 
5. Sélectionnez le pipeline de build qui publie le package à déployer sur l’ordinateur virtuel. Notez que le script de déploiement _deploy.ps1_ ou _deploy.sh_ doit se trouver dans le dossier _deployscripts_ qui est situé à la racine du package publié. Ce script de déploiement sera exécuté par le pipeline Azure DevOps au moment de l’exécution.
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

15. Par défaut, la tâche Execute Deploy Script (Exécuter le script de déploiement) va exécuter par défaut le script de déploiement _deploy.ps1_ ou _deploy.sh_ qui se trouve dans le dossier _deployscripts_ situé dans le répertoire racine du package publié.
  
**Déploiements Canary** : Un déploiement Canary réduit les risques en déployant lentement les modifications pour un petit sous-ensemble d’utilisateurs. À mesure que se renforce votre confiance en la nouvelle version, vous pouvez commencer à la mettre en production sur davantage de serveurs de votre infrastructure et y router plus d’utilisateurs. Vous pouvez configurer des déploiements canari sur vos « **machines virtuelles** » via le portail Azure à l’aide de l’option de livraison continue. Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Suivez les étapes 2 à 7 de la section **Déploiements Rolling** pour ajouter plusieurs machines virtuelles au groupe de déploiement. Dans la liste déroulante des stratégies de déploiement, sélectionnez « Canary ».
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Ajoutez l’étiquette « canary » aux machines virtuelles qui doivent faire partie des déploiements Canary, et l’étiquette « prod » aux machines virtuelles qui feront partie du déploiement une fois le déploiement Canary effectué.
4. Une fois l’opération effectuée, vous disposez d’un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Comme dans la section **Déploiements Rolling**, vous pouvez cliquer sur **Modifier** dans Azure DevOps pour voir la configuration du pipeline de mise en production. Le pipeline comprend trois phases. La première est une phase de déploiement de groupe durant laquelle sont déployées les machines virtuelles portant l’étiquette _canary_. La deuxième phase suspend le pipeline et attend une intervention manuelle pour reprendre l’exécution. Une fois qu’un utilisateur est sûr que le déploiement Canary est stable, il peut reprendre l’exécution du pipeline qui exécutera ensuite la troisième phase, durant laquelle sont déployées les machines virtuelles portant l’étiquette _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Déploiement Blue-Green** : Le déploiement Blue-Green réduit les temps d’arrêt à l’aide d’un environnement de secours identique. À tout moment, l’un des environnements est actif. Pendant la préparation d’une nouvelle mise en production, vous effectuez votre dernière étape de tests dans l’environnement vert. Une fois que le logiciel fonctionne dans l’environnement vert, changez le trafic pour router toutes les requêtes entrantes en direction de l’environnement vert, l’environnement bleu étant désormais inactif.
Vous pouvez configurer des déploiements bleu/vert sur vos « **machines virtuelles** » à partir du portail Azure via l’option de livraison continue. 

Voici la procédure pas à pas.

1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Suivez les étapes 2 à 7 de la section **Déploiements Rolling** pour ajouter plusieurs machines virtuelles au groupe de déploiement. Ajoutez une étiquette « blue » ou « green » aux machines virtuelles qui doivent faire partie des déploiements Blue-Green. Si la machine virtuelle est destinée à un rôle de secours, vous devez lui ajouter l’étiquette « green ».
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Une fois l’opération effectuée, vous disposez d’un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Comme avec les **déploiements Rolling**, vous pouvez cliquer sur **Modifier** dans Azure DevOps pour voir la configuration du pipeline de mise en production. Le pipeline comprend trois phases. La première est une phase de déploiement de groupe durant laquelle sont déployées les machines virtuelles portant l’étiquette _green_ (machines virtuelles de secours). La deuxième phase suspend le pipeline et attend une intervention manuelle pour reprendre l’exécution. Une fois que l’utilisateur a vérifié que le déploiement est stable, il peut désormais rediriger le trafic vers les machines virtuelles _green_ et reprendre l’exécution du pipeline. L’étiquette _blue_ devient alors une étiquette _green_ dans les machines virtuelles. Vous garantissez ainsi que les machines virtuelles dotées d’une version antérieure de l’application porteront l’étiquette _green_ et qu’elles seront déployées lors de la prochaine exécution du pipeline.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Notez que cette stratégie de déploiement nécessite au moins une machine virtuelle « blue » et une machine virtuelle « green ». Avant de reprendre l’exécution du pipeline à l’aide d’une intervention manuelle, vérifiez que vous disposez bien d’une machine virtuelle _blue_.





 
## <a name="azure-devops-project"></a>Azure DevOps Projects 
Découvrez comment bien démarrer avec Azure plus facilement que jamais.
 
Avec DevOps Projects, commencez à exécuter votre application sur n’importe quel service Azure en seulement trois étapes : sélectionnez un langage d’application, un runtime et un service Azure.
 
[Plus d’informations](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Ressources supplémentaires 
- [Déployer sur des machines virtuelles Azure à l’aide de DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implémenter un déploiement continu de votre application sur un groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
