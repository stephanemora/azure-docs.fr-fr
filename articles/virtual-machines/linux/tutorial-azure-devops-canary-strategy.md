---
title: Tutoriel – Configurer des déploiements canary pour les machines virtuelles Linux Azure
description: Dans ce tutoriel, vous allez découvrir comment configurer un pipeline de déploiement continu (CD) qui met à jour un groupe de machines virtuelles Linux Azure en utilisant une stratégie de déploiement canary
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120527"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutoriel – Configurer une stratégie de déploiement Canary pour des machines virtuelles Linux Azure


## <a name="iaas---configure-cicd"></a>IaaS - Configurer les outils CI/CD 
Azure Pipelines fournit un ensemble complet d’outils d’automatisation CI/CD pour les déploiements sur des machines virtuelles. Vous pouvez configurer un pipeline de livraison continue pour une machine virtuelle Azure directement à partir du portail Azure. Ce document contient les étapes associées à la configuration d’un pipeline CI/CD qui utilise la stratégie Canary pour des déploiements multimachines. Vous pouvez aussi vous pencher sur d’autres stratégies comme [rolling](https://aka.ms/AA7jlh8) et [blue-green](https://aka.ms/AA83fwu), qui sont prises en charge en l’état sur le portail Azure. 


**Configurer les outils CI/CD sur les machines virtuelles**

Les machines virtuelles peuvent être ajoutées en tant que cibles à un [groupe de déploiement](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups), et peuvent être ciblées pour des mises à jour multimachines. Une fois le déploiement effectué, l’**historique de déploiement** de votre groupe de déploiement offre une traçabilité entre la machine virtuelle et le pipeline, puis entre le pipeline et la validation. 
 
  
**Déploiements Canary** : Un déploiement Canary réduit les risques en déployant lentement les modifications pour un petit sous-ensemble d’utilisateurs. À mesure que se renforce votre confiance en la nouvelle version, vous pouvez commencer à la mettre en production sur davantage de serveurs de votre infrastructure et y router plus d’utilisateurs. Vous pouvez configurer des déploiements canary sur vos machines virtuelles via le portail Azure à l’aide de l’option de livraison continue. Voici la procédure pas à pas. 
1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Dans le volet gauche de la machine virtuelle, accédez au menu  **Livraison continue** . Cliquez sur **Configurer**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Dans le volet de configuration, cliquez sur **Organisation Azure DevOps** pour sélectionner un compte existant ou en créer un. Sélectionnez ensuite le projet dans lequel vous souhaitez configurer le pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un groupe de déploiement est un ensemble logique de machines cibles de déploiement qui représentent les environnements physiques, par exemple « Dev », « Test », « UAT » et « Production ». Vous pouvez créer un groupe de déploiement, ou en sélectionner un qui existe déjà. 
5. Sélectionnez le pipeline de build qui publie le package à déployer sur la machine virtuelle. Notez que le script de déploiement _deploy.ps1_ ou _deploy.sh_ doit se trouver dans le dossier `deployscripts` à la racine du package publié. Ce script de déploiement sera exécuté par le pipeline Azure DevOps au moment de l’exécution.
6. Sélectionnez la stratégie de déploiement de votre choix. Sélectionnez **Canary**.
7. Ajoutez l’étiquette « canary » aux machines virtuelles qui doivent faire partie des déploiements Canary, et l’étiquette « prod » aux machines virtuelles qui feront partie du déploiement une fois le déploiement Canary effectué. Les étiquettes vous permettent de cibler uniquement les machines virtuelles qui ont un rôle spécifique.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Cliquez sur **OK** dans la boîte de dialogue pour configurer le pipeline de livraison continue. Vous disposez maintenant d’un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Cliquez sur **Modifier** dans Azure DevOps pour voir la configuration du pipeline de mise en production. Le pipeline comprend trois phases. La première est une phase de déploiement de groupe durant laquelle sont déployées les machines virtuelles portant l’étiquette _canary_. La deuxième phase suspend le pipeline et attend une intervention manuelle pour reprendre l’exécution. Une fois satisfait de la stabilité du déploiement canary, un utilisateur peut reprendre l’exécution du pipeline qui exécutera alors la troisième phase, au cours de laquelle les machines virtuelles portant l’étiquette _prod_ seront déployées. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Avant de reprendre l’exécution du pipeline, vérifiez qu’au moins une machine virtuelle porte l’étiquette _prod_. Dans la troisième phase du pipeline, l’application n’est déployée que sur les machines virtuelles portant l’étiquette _prod_.

11. Par défaut, la tâche Execute Deploy Script (Exécuter le script de déploiement) exécute le script de déploiement _deploy.ps1_ ou _deploy.sh_ qui se trouve dans le dossier « deployscripts » situé dans le répertoire racine du package publié. Vérifiez que le pipeline de build sélectionné publie ceci dans le dossier racine du package. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Autres stratégies de déploiement
- [Configurer une stratégie de déploiement Rolling](https://aka.ms/AA7jlh8)
- [Configurer une stratégie de déploiement Blue-Green](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps Projects 
Découvrez comment bien démarrer avec Azure plus facilement que jamais.
 
Avec DevOps Projects, commencez à exécuter votre application sur n’importe quel service Azure en seulement trois étapes : sélectionnez un langage d’application, un runtime et un service Azure.
 
[Plus d’informations](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Ressources supplémentaires 
- [Déployer sur des machines virtuelles Azure à l’aide de DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implémenter un déploiement continu de votre application sur un groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
