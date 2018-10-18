---
title: Tutoriel Déployer un travail Azure Stream Analytics avec CI/CD à l’aide d’Azure DevOps Services
description: Cet article décrit le déploiement d’un travail Stream Analytics avec CI/CD à l’aide d’Azure DevOps Services.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: adacbaf718c5ef293b4ee3fa833083704aa41f5c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297940"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Tutoriel : Déployer un travail Azure Stream Analytics avec CI/CD à l’aide d’Azure Pipelines
Ce tutoriel explique comment configurer l’intégration et le déploiement continus pour un travail Azure Stream Analytics à l’aide d’Azure Pipelines. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter le contrôle de code source à votre projet
> * Créer un pipeline de build dans Azure Pipelines
> * Créer un pipeline de mise en production dans Azure Pipelines
> * Déployer et mettre à niveau une application automatiquement

## <a name="prerequisites"></a>Prérequis
Avant de commencer, veillez à disposer des éléments qui suivent :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installez [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) et les charges de travail **Développement Azure** ou **Stockage et traitement de données**.
* Créez un [projet Stream Analytics dans Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Créez une organisation [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Configurer une dépendance de package NuGet
Pour procéder à une génération et à un déploiement automatiques sur une machine arbitraire, vous devez utiliser le package NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Il fournit MSBuild, une série locale et des outils de déploiement qui prennent en charge le processus de déploiement et d’intégration continus des projets Visual Studio Stream Analytics. Pour plus d’informations, voir [Intégrer et développer en continu avec les outils Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Ajoutez **packages.config** au répertoire du projet.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Partager votre solution Visual Studio dans un nouveau référentiel Git Azure Repos

Partagez les fichiers source de votre application dans un projet Azure DevOps pour pouvoir générer des builds.  

1. Créez un référentiel Git local pour votre projet en sélectionnant **Ajouter au contrôle de code source**, puis **Git** dans la barre d’état située dans le coin inférieur droit de Visual Studio. 

2. Dans la vue **Synchronisation** de **Team Explorer**, sélectionnez le bouton **Publier le référentiel Git** sous **Push sur Azure DevOps Services**.

   ![Envoyer (push) le référentiel Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Vérifiez votre adresse e-mail et sélectionnez votre organisation dans la liste déroulante **Domaine Azure DevOps Services**. Entrez le nom de votre dépôt et sélectionnez **Publier le dépôt**.

   ![Envoi (push) du dépôt Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    La publication du référentiel entraîne la création d’un projet dans votre organisation portant le même nom que le référentiel local. Pour créer le référentiel dans un projet existant, cliquez sur **Avancé** en regard de **Nom du référentiel**, puis sélectionnez un projet. Vous pouvez afficher votre code dans le navigateur en sélectionnant **See it on the web** (Le visualiser sur le web).
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Configurer la livraison continue avec Azure DevOps
Un pipeline de build Azure Pipelines décrit un flux de travail qui se compose d’étapes de génération exécutées séquentiellement. En savoir plus sur [les pipelines de build Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Un pipeline de mise en production Azure Pipelines décrit un flux de travail qui déploie un package d’application sur un cluster. Lorsqu’ils sont utilisés ensemble, le pipeline de build et le pipeline de mise en production exécutent le flux de travail dans son ensemble, depuis les fichiers source jusqu’à l’exécution de l’application dans votre cluster. En savoir plus sur les [pipelines de mise en production](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Créer un pipeline de build
Ouvrez un navigateur web et accédez au projet que vous venez de créer dans [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Dans l’onglet **Build et mise en production**, sélectionnez **Builds**, puis **+Nouveau**.  Sélectionnez **Azure DevOps Services Git** et **Continuer**.
    
    ![Sélectionner une source](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. Dans **Sélectionner un modèle**, cliquez sur **Processus vide** pour démarrer avec un pipeline vide.
    
    ![Choisir le modèle de build](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Sous **Déclencheurs**, activez l’intégration continue en cochant l’état du déclencheur **Activer l’intégration continue**.  Sélectionnez **Enregistrer et mettre en file d’attente** pour lancer une build manuellement. 
    
    ![État du déclencheur](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Les builds sont également déclenchées au moment de l’archivage ou de l’envoi (push). Pour vérifier la progression de votre build, basculez vers l’onglet **Builds**.  Une fois que vous avez vérifié que la build s’exécute correctement, vous devez définir un pipeline de mise en production assurant le déploiement de votre application sur un cluster. Cliquez avec le bouton droit sur les points de suspension situés en regard de votre pipeline de build, puis sélectionnez **Modifier**.

5.  Dans **Tâches**, entrez « Hébergée » pour **File d’attente d’agents**.
    
    ![Sélectionner une file d’attente d’agents](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Dans **Phase 1**, cliquez sur **+** et ajoutez une tâche **NuGet**.
    
    ![Ajouter la tâche NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Développez **Avancé** et ajoutez `$(Build.SourcesDirectory)\packages` dans le **Répertoire de destination**. Conservez les valeurs de configuration de NuGet par défaut restantes.

   ![Configurer une tâche NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Dans **Phase 1**, cliquez sur **+** et ajoutez une tâche **MSBuild**.

   ![Ajouter la tâche MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Modifiez les **Arguments MSBuild** comme suit :

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurer la tâche MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Dans **Phase 1**, cliquez sur **+** et ajoutez une tâche **Déploiement d’un groupe de ressources Azure**. 
    
    ![Ajouter une tâche Déploiement d’un groupe de ressources Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Développez **Détails relatifs à Azure** et renseignez la configuration comme suit :
    
    |**Paramètre**  |**Valeur suggérée**  |
    |---------|---------|
    |Abonnement  |  Choisissez votre abonnement.   |
    |Action  |  Créer ou mettre à jour un groupe de ressources   |
    |Groupe de ressources  |  Entrez un nom de groupe de ressources.   |
    |Modèle  | [Chemin d’accès de votre solution]\bin\Debug\Deploy\\[Nom de votre projet].JobTemplate.json   |
    |Paramètres de modèle  | [Chemin d’accès de votre solution]\bin\Debug\Deploy\\[Nom de votre projet].JobTemplate.parameters.json   |
    |Remplacer les paramètres du modèle  | Tapez les paramètres du modèle à remplacer dans la zone de texte. Exemple : – storageName fabrikam – adminUsername $(vmusername) - adminPassword $(password) – azureKeyVaultName $(fabrikamFibre). Cette propriété est facultative, mais votre build provoquera des erreurs si les paramètres clés ne sont pas remplacés.    |
    
    ![Définir des propriétés](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Cliquez sur **Enregistrer et mettre en file d’attente** pour tester le pipeline de build.
    
    ![Définir les paramètres de remplacement](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Échec du processus de génération
Vous pouvez recevoir des erreurs pour des paramètres de déploiement de valeur Null si vous n’avez pas remplacé des paramètres de modèle dans la tâche **Déploiement d’un groupe de ressources Azure** de votre pipeline de build. Retournez dans le pipeline de build et remplacez les paramètres de valeur Null pour résoudre l’erreur.

   ![Échec du processus de génération](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Valider et envoyer les modifications pour déclencher une mise en production
Vérifiez que le pipeline d’intégration continue fonctionne en archivant certaines modifications de code dans Azure DevOps.    

Lorsque vous écrivez votre code, vos modifications sont suivies automatiquement par Visual Studio. Validez les modifications dans votre référentiel Git local en sélectionnant l’icône de modifications en attente dans la barre d’état située en bas à droite.

1. Dans la vue **Modifications** de Team Explorer, ajoutez un message décrivant votre mise à jour, puis validez vos modifications.

    ![Valider et envoyer (push) les changements](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Sélectionnez l’icône de modifications non publiées dans la barre d’état ou la vue Synchronisation de Team Explorer. Sélectionnez **Envoi (push)** pour mettre à jour votre code dans Azure DevOps.

    ![Valider et envoyer (push) les changements](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

L’envoi (push) des modifications à Azure DevOps Services déclenche automatiquement une build.  Une fois le pipeline de build terminé, une mise en production est créée automatiquement et commence la mise à jour du travail sur le cluster.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez l’arrêter et le redémarrer plus tard lorsque vous en avez besoin. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées au cours de ce didacticiel en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée.  
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des outils Azure Stream Analytics pour Visual Studio afin de configurer un processus de déploiement et d’intégration continus, passez à l’article relatif à la configuration d’un pipeline CI/CD :

> [!div class="nextstepaction"]
> [Intégrer et développer en continu avec les outils Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
