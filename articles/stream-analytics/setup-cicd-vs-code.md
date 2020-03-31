---
title: Déployer un travail Azure Stream Analytics à l'aide du package npm CI/CD
description: Cet article explique comment utiliser le package npm CI/CD Azure Stream Analytics pour configurer un processus de déploiement et d’intégration continus.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962173"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Déployer un travail Azure Stream Analytics à l'aide du package npm CI/CD 

Vous pouvez utiliser le package npm CI/CD Azure Stream Analytics afin de configurer un processus de déploiement et d’intégration continus pour vos travaux Stream Analytics. Cet article décrit comment utiliser le package npm avec n’importe quel système CI/CD, ainsi que les instructions spécifiques au déploiement avec Azure Pipelines.

Pour plus d’informations sur le déploiement avec PowerShell, consultez [Effectuer un déploiement avec un fichier de modèle Resource Manager et Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Vous pouvez également consulter [Utiliser un objet en tant que paramètre dans un modèle Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Générer le projet VS Code

Vous pouvez activer l’intégration et le déploiement continus pour les tâches Azure Stream Analytics à l’aide du package npm **asa-streamanalytics-cicd**. Le package npm fournit les outils pour générer les modèles Azure Resource Manager des projets [Stream Analytics pour Visual Studio Code](quick-create-vs-code.md). Il peut être utilisé avec Windows, macOS et Linux sans installer Visual Studio Code.

Vous pouvez [télécharger le package](https://www.npmjs.com/package/azure-streamanalytics-cicd) directement, ou l’installer [globalement](https://docs.npmjs.com/downloading-and-installing-packages-globally) via la commande `npm install -g azure-streamanalytics-cicd`. Il s’agit de l’approche recommandée, qui peut également être utilisée dans une tâche de script PowerShell ou Azure CLI de pipeline de build dans **Azure Pipelines**.

Une fois que vous avez installé le package, utilisez la commande suivante pour générer les modèles Azure Resource Manager. L’argument **scriptPath** est le chemin d’accès complet pour le fichier **asaql** dans votre projet. Assurez-vous que les fichiers asaproj.json et JobConfig.json se trouvent dans le même dossier avec le fichier de script. Si l’argument **outputPath** n’est pas spécifié, les modèles seront placés dans le dossier **Deploy** sous le dossier **bin** du projet.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemple (sur macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quand un projet Stream Analytics pour Visual Studio Code est correctement généré, il crée les deux fichiers de modèles Azure Resource Manager suivants sous le dossier **bin/[Debug/Retail]/Deploy** : 

*  Fichier de modèle Resource Manager

       [ProjectName].JobTemplate.json 

*  Fichier de paramètres Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Les paramètres par défaut dans le fichier parameters.json proviennent des paramètres de votre projet Visual Studio Code. Si vous voulez effectuer un déploiement dans un autre environnement, remplacez les paramètres en conséquence.

> [!NOTE]
> Pour toutes les informations d’identification, les valeurs par défaut sont Null. Vous **devez** les définir avant le déploiement dans le cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Déployer avec Azure Pipelines

Cette section explique en détail comment créer des pipelines de [build](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) et de [mise en production](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) Azure Pipelines à l’aide de npm.

Ouvrez un navigateur web et accédez à votre projet Azure Stream Analytics Visual Studio Code.

1. Sous **Pipelines** dans le menu de navigation gauche, sélectionnez **Builds**. Sélectionnez ensuite **Nouveau pipeline**.

   ![Créer un pipeline Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Sélectionnez **Utiliser l’éditeur classique** pour créer un pipeline sans YAML.

3. Sélectionnez votre type de source, votre projet d’équipe et votre référentiel. Sélectionnez **Continuer**.

   ![Sélectionner un projet Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Dans la page **Sélectionner un modèle**, sélectionnez **Projet vide**.

### <a name="add-npm-task"></a>Ajouter une tâche npm

1. Dans la page **Tâches**, sélectionnez le signe plus en regard de **Travail d’agent 1**. Entrez « npm » dans la recherche de tâches et sélectionnez **npm**.

   ![Sélectionner une tâche npm](./media/setup-cicd-vs-code/search-npm.png)

2. Donnez à la tâche un **nom d’affichage**. Définissez l'option **Commande** sur *Personnalisée*, puis entrez la commande suivante dans **Commande et arguments**. Conservez les options par défaut restantes.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Entrer les configurations pour la tâche npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Ajouter une tâche de ligne de commande

1. Dans la page **Tâches**, sélectionnez le signe plus en regard de **Travail d’agent 1**. Recherchez **Ligne de commande**.

2. Donnez à la tâche un **nom d’affichage** et entrez le script suivant. Modifiez le script avec le nom de votre référentiel et le nom de votre projet.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Entrer les configurations pour la tâche de ligne de commande](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Ajouter une tâche de copie des fichiers

1. Dans la page **Tâches**, sélectionnez le signe plus en regard de **Travail d’agent 1**. Recherchez **Copier des fichiers**. Entrez ensuite les configurations suivantes.

   |Paramètre|Entrée|
   |-|-|
   |Nom complet|Copier des fichiers vers : $(build.artifactstagingdirectory)|
   |Dossier source|`$(system.defaultworkingdirectory)`| 
   |Contents| `**\Deploy\**` |
   |Dossier cible| `$(build.artifactstagingdirectory)`|

   ![Entrer les configurations pour la tâche de copie](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Ajouter une tâche Publier des artefacts de build

1. Dans la page **Tâches**, sélectionnez le signe plus en regard de **Travail d’agent 1**. Recherchez **Publier des artefacts de build** et sélectionnez l’option accompagnée d'une icône de flèche noire. 

2. Ne modifiez aucune configuration par défaut.

### <a name="save-and-run"></a>Enregistrer et exécuter

Après avoir ajouté les tâches npm, ligne de commande, copier les fichiers et publier les artefacts de build, sélectionnez **Enregistrer et mettre en file d'attente**. Lorsque vous y êtes invité, entrez un commentaire d'enregistrement et sélectionnez **Enregistrer et exécuter**.

## <a name="release-with-azure-pipelines"></a>Mettre en production avec Azure Pipelines

Ouvrez un navigateur web et accédez à votre projet Azure Stream Analytics Visual Studio Code.

1. Sous **Pipelines** dans le menu de navigation gauche, sélectionnez **Mises en production**. Sélectionnez ensuite **Nouveau pipeline**.

2. Sélectionnez **Démarrer par une tâche vide**.

3. Dans la zone **Artefacts**, sélectionnez **+ Ajouter un artefact**. Sous **Source**, sélectionnez le pipeline de build que vous venez de créer, puis **Ajouter**.

   ![Entrer un artefact de pipeline de build](./media/setup-cicd-vs-code/build-artifact.png)

4. Remplacez le nom de **Étape 1** par **Travail de déploiement dans l’environnement de test**.

5. Ajoutez une nouvelle étape et nommez-la **Travail de déploiement dans l'environnement de production**.

### <a name="add-tasks"></a>Ajouter des tâches

1. Dans la liste déroulante des tâches, sélectionnez **Travail de déploiement dans l’environnement de test**. 

2. Sélectionnez **+** en regard de **Travail d'agent** et recherchez *Déploiement du groupe de ressources Azure*. Entrez les paramètres suivants :

   |Paramètre|Valeur|
   |-|-|
   |Nom complet| *Déployer myASAJob*|
   |Abonnement Azure| Choisissez votre abonnement.|
   |Action| *Créer ou mettre à jour un groupe de ressources*|
   |Resource group| Choisissez un nom pour le groupe de ressources de test qui contiendra votre travail Stream Analytics.|
   |Emplacement|Sélectionnez l’emplacement de votre groupe de ressources de test.|
   |Emplacement du modèle| *Artefact lié*|
   |Modèle| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Paramètres de modèle|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Remplacer les paramètres du modèle|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Mode de déploiement|Incrémentiel|

3. Dans la liste déroulante des tâches, sélectionnez **Travail de déploiement dans l’environnement de production**.

4. Sélectionnez **+** en regard de **Travail d'agent** et recherchez *Déploiement du groupe de ressources Azure*. Entrez les paramètres suivants :

   |Paramètre|Valeur|
   |-|-|
   |Nom complet| *Déployer myASAJob*|
   |Abonnement Azure| Choisissez votre abonnement.|
   |Action| *Créer ou mettre à jour un groupe de ressources*|
   |Resource group| Choisissez un nom pour le groupe de ressources de production qui contiendra votre travail Stream Analytics.|
   |Emplacement|Sélectionnez l’emplacement de votre groupe de ressources de production.|
   |Emplacement du modèle| *Artefact lié*|
   |Modèle| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Paramètres de modèle|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Remplacer les paramètres du modèle|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Mode de déploiement|Incrémentiel|

### <a name="create-release"></a>Créer une mise en production

Pour créer une mise en production, sélectionnez **Créer une mise en production** dans le coin supérieur droit.

![Créer une mise en production à l'aide d'Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour utiliser une identité managée pour Azure Data Lake Store Gen1 comme récepteur de sortie, vous devez fournir l’accès au principal de service à l’aide de PowerShell avant de déployer sur Azure. Découvrez plus d’informations sur le [déploiement d’ADLS Gen1 avec une identité managée et le modèle Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un travail cloud Azure Stream Analytics dans Visual Studio Code (préversion)](quick-create-vs-code.md)
* [Tester des requêtes Stream Analytics localement avec Visual Studio Code (Préversion)](visual-studio-code-local-run.md)
* [Explorer Azure Stream Analytics avec Visual Studio Code (préversion)](visual-studio-code-explore-jobs.md)
