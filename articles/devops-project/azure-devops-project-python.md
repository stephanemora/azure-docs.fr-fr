---
title: 'Démarrage rapide : Créer un pipeline CI/CD pour Python avec Azure DevOps Projects'
description: DevOps Projects facilite la prise en main d’Azure. Ce composant vous aide à lancer une application sur le service Azure de votre choix en quelques étapes rapides.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 90af443621ec47400779bdc1689fc07be558e4b8
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164538"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>Créer un pipeline CI/CD pour Python avec Azure DevOps Projects

Azure DevOps Projects présente une expérience simplifiée qui crée des ressources Azure et configure un pipeline d’intégration continue (CI) et de livraison continue (CD) pour votre application Python.  

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

 DevOps Projects crée un pipeline CI/CD dans Azure Pipelines.  Vous pouvez créer gratuitement une organisation Azure DevOps ou utiliser une organisation existante. DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

2. Choisissez l’icône **Créer une ressource** dans le volet gauche, puis recherchez **DevOps Projects**.  

3. Sélectionnez **Créer**.

    ![Démarrage de la configuration de la livraison continue](_img/azure-devops-project-python/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez l’exemple d’application Python. Les exemples Python offrent le choix entre plusieurs infrastructures d'application.

1. L’exemple de framework par défaut est Django. Laissez le paramètre par défaut, puis sélectionnez **Suivant**.    
Web App pour conteneurs est la cible de déploiement par défaut. Le framework d’application, que vous avez choisi précédemment, détermine le type de cible de déploiement de service Azure disponible ici. 

3. Laissez le service par défaut, puis sélectionnez **Suivant**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurer Azure DevOps et un abonnement Azure 

1. Créez une organisation Azure DevOps ou choisissez une organisation existante. 

    a. Entrez un nom pour votre projet dans Azure DevOps.  

    b. Sélectionnez votre abonnement et un emplacement Azure, entrez un nom pour votre application, puis sélectionnez **Terminé**.  
     Après quelques minutes, le tableau de bord du projet est affiché dans le portail Azure. Un exemple d’application est configuré dans un dépôt de votre organisation Azure DevOps, une build est exécutée et votre application est déployée sur Azure. Ce tableau de bord donne une visibilité sur votre dépôt de code, votre pipeline CI/CD et votre application dans Azure.  
    
2. Sélectionnez **Parcourir** pour voir votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-python/dashboardnopreview.png) 
    
 DevOps Projects configure automatiquement un déclencheur de build et de mise en production CI. Vous êtes maintenant prêt à collaborer avec une équipe sur une application Python avec un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre travail le plus récent sur votre site web.

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

 DevOps Projects crée un dépôt Git dans Azure Repos ou dans GitHub. Pour voir le dépôt et modifier le code dans votre application, suivez les étapes ci-dessous : 

1. À gauche du tableau de bord DevOps Projects, sélectionnez le lien correspondant à votre branche master.  
        Ce lien ouvre une vue sur le dépôt Git récemment créé.

1. Pour afficher l’URL du clone du dépôt, sélectionnez **Clone** en haut à droite du navigateur.   
Vous pouvez cloner votre dépôt Git dans votre environnement de développement intégré favori.  Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications de code directement dans la branche master et les valider.

1. À gauche, accédez au fichier **app/templates/app/index.html**.

1. Sélectionnez **Modifier** et modifiez le texte. Par exemple, modifiez l’une des balises div.

1. Sélectionnez **Valider**, puis enregistrez vos modifications.

1. Dans votre navigateur, accédez au tableau de bord DevOps Projects.   
    Vous devez maintenant voir une build en cours. Les modifications que vous venez d’effectuer font automatiquement l’objet d’une build et d’un déploiement via un pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examiner le pipeline CI/CD

Dans l’étape précédente, DevOps Projects a automatiquement configuré un pipeline CI/CD complet. Explorez et personnalisez le pipeline selon vos besoins. Pour vous familiariser avec les pipelines de build et de mise en production, effectuez les étapes suivantes :

1. En haut du tableau de bord DevOps Projects, sélectionnez **Pipelines de build**.  
Un onglet du navigateur affiche le pipeline de build de votre nouveau projet.

1. Pointez sur le champ **État**, puis sélectionnez les **points de suspension** (...).  
        Un menu affiche plusieurs options, comme la mise en file d’attente d’une nouvelle build, la mise en pause d’une build et la modification du pipeline de build.

1. Sélectionnez **Modifier**.

1. Dans ce volet, vous pouvez examiner les différentes tâches de votre pipeline de build.  
        La build effectue différentes tâches, comme la récupération (fetch) de sources dans le dépôt Git, la restauration de dépendances et la publication des sorties pour les déploiements.

1. En haut du pipeline de build, sélectionnez le nom du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, sélectionnez **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  
        Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps fait le suivi des modifications apportées au pipeline de build et vous permet de comparer des versions.

1. Sélectionnez **Déclencheurs**.  
         DevOps Projects crée automatiquement un déclencheur CI, et chaque validation dans le dépôt lance une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  
        En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis choisissez **Versions**.   
 DevOps Projects crée un pipeline de mise en production pour gérer les déploiements sur Azure.

1. Sélectionnez les points de suspension en regard de votre pipeline de mise en production, puis sélectionnez **Modifier**.  
Le pipeline de mise en production définit le processus de mise en production.  
        
12. Sous **Artefacts**, sélectionnez **Déposer**.   
Le pipeline de build que vous avez examiné dans les étapes précédentes produit la sortie utilisée pour l’artefact. 

1. En regard de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  
        Le pipeline de mise en production a un déclencheur CD actif, qui effectue un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur, vos déploiements nécessitant alors une exécution manuelle. 

1. Sur la gauche, sélectionnez **Tâches**.   
Les tâches sont les activités effectuées par votre processus de déploiement. Dans cet exemple, une tâche a été créée pour déployer sur Azure App Service.

1. À droite, sélectionnez **Afficher les mises en production** pour voir l’historique des mises en production.  
        
1. Sélectionnez les points de suspension (...) en regard d’une de vos mises en production, puis sélectionnez **Ouvrir**.  
        Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**. 
        Cette vue montre les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.   
Les journaux contiennent des informations utiles sur le processus de déploiement. Vous pouvez les voir à la fois pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer Azure App Service et les ressources associées. Utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Quand vous avez configuré votre processus CI/CD, les pipelines de build et de mise en production ont été créés automatiquement. Vous pouvez le modifier afin qu’il réponde aux besoins de votre équipe. Pour en savoir plus sur le pipeline CI/CD, consultez :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
