---
title: Tutoriel – Configurer des déploiements canary pour les machines virtuelles Linux Azure
description: Dans ce tutoriel, vous allez découvrir comment configurer un pipeline de déploiement continu (CD) qui met à jour un groupe de machines virtuelles Azure en utilisant une stratégie de déploiement blue-green
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120547"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutoriel – Configurer une stratégie de déploiement Blue-Green pour des machines virtuelles Linux Azure


## <a name="iaas---configure-cicd"></a>IaaS - Configurer les outils CI/CD 
Azure Pipelines fournit un ensemble complet d’outils d’automatisation CI/CD pour les déploiements sur des machines virtuelles. Vous pouvez configurer un pipeline de livraison continue pour une machine virtuelle Azure directement à partir du portail Azure. Ce document contient les étapes associées à la configuration d’un pipeline CI/CD qui utilise la stratégie blue-green pour des déploiements multimachines. Vous pouvez aussi vous pencher sur d’autres stratégies comme [rolling](https://aka.ms/AA7jlh8) et [canary](https://aka.ms/AA7jdrz), qui sont prises en charge en l’état sur le portail Azure. 

 
 **Configurer les outils CI/CD sur les machines virtuelles**

Les machines virtuelles peuvent être ajoutées en tant que cibles à un [groupe de déploiement](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups), et peuvent être ciblées pour des mises à jour multimachines. Une fois le déploiement effectué, l’**historique de déploiement** d’un groupe de déploiement offre une traçabilité entre la machine virtuelle et le pipeline, puis entre le pipeline et la validation. 
 
  
**Déploiement Blue-Green** : Le déploiement Blue-Green réduit les temps d’arrêt à l’aide d’un environnement de secours identique. À tout moment, l’un des environnements est actif. Pendant la préparation d’une nouvelle mise en production, vous effectuez la dernière étape de tests dans l’environnement « green ». Une fois que le logiciel fonctionne dans l’environnement vert, changez le trafic pour router toutes les requêtes entrantes en direction de l’environnement vert, l’environnement bleu étant désormais inactif.
Vous pouvez configurer des déploiements bleu/vert sur vos « **machines virtuelles** » à partir du portail Azure via l’option de livraison continue. 

Voici la procédure pas à pas.

1. Connectez-vous à votre portail Azure, puis accédez à une machine virtuelle 
2. Dans le volet gauche de la machine virtuelle, accédez à **Livraison continue**. Puis cliquez sur **Configurer**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Dans le volet de configuration, cliquez sur **Organisation Azure DevOps** pour sélectionner un compte existant ou en créer un. Sélectionnez ensuite le projet dans lequel vous souhaitez configurer le pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un groupe de déploiement est un ensemble logique de machines cibles de déploiement qui représentent les environnements physiques, par exemple « Dev », « Test », « UAT » et « Production ». Vous pouvez créer un groupe de déploiement, ou en sélectionner un qui existe déjà. 
5. Sélectionnez le pipeline de build qui publie le package à déployer sur la machine virtuelle. Notez que le script de déploiement _deploy.ps1_ ou _deploy.sh_ doit se trouver dans le dossier `deployscripts` à la racine du package publié. Ce script de déploiement sera exécuté par le pipeline Azure DevOps au moment de l’exécution.
6. Sélectionnez la stratégie de déploiement de votre choix. Sélectionnez **Blue-Green**.
7. Ajoutez une étiquette « blue » ou « green » aux machines virtuelles qui doivent faire partie des déploiements Blue-Green. Si la machine virtuelle est destinée à un rôle de secours, vous devez lui ajouter l’étiquette « green ». Sinon, ajoutez-lui l’étiquette « blue ».
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Cliquez sur **OK** pour configurer le pipeline de livraison continue. Vous avez maintenant un pipeline de livraison continue configuré pour un déploiement sur la machine virtuelle.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Cliquez sur **Modifier** dans Azure DevOps pour voir la configuration du pipeline de mise en production. Le pipeline comprend trois phases. La première est une phase de déploiement de groupe durant laquelle sont déployées les machines virtuelles portant l’étiquette _green_ (machines virtuelles de secours). La deuxième phase suspend le pipeline et attend une intervention manuelle pour reprendre l’exécution. Une fois qu’il est satisfait de la stabilité du déploiement, l’utilisateur peut ensuite rediriger le trafic vers les machines virtuelles _green_ et reprendre l’exécution du pipeline. L’étiquette _blue_ devient alors une étiquette _green_ dans les machines virtuelles. Vous garantissez ainsi que les machines virtuelles dotées d’une version antérieure de l’application porteront l’étiquette _green_ et qu’elles seront déployées lors de la prochaine exécution du pipeline.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Par défaut, la tâche Execute Deploy Script (Exécuter le script de déploiement) exécute le script de déploiement _deploy.ps1_ ou _deploy.sh_ qui se trouve dans le dossier `deployscripts` situé dans le répertoire racine du package publié. Vérifiez que le pipeline de build sélectionné publie ceci dans le dossier racine du package.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Autres stratégies de déploiement
- [Configurer une stratégie de déploiement Rolling](https://aka.ms/AA7jlh8)
- [Configurer une stratégie de déploiement Canary](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps Projects 
Découvrez comment bien démarrer avec Azure plus facilement que jamais.
 
Avec DevOps Projects, commencez à exécuter votre application sur n’importe quel service Azure en seulement trois étapes : sélectionnez un langage d’application, un runtime et un service Azure.
 
[Plus d’informations](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Ressources supplémentaires 
- [Déployer sur des machines virtuelles Azure à l’aide de DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implémenter un déploiement continu de votre application sur un groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
