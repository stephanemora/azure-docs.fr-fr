---
title: Utiliser le package npm CI/CD Azure Stream Analytics
description: Cet article explique comment utiliser le package npm CI/CD Azure Stream Analytics pour configurer un processus de déploiement et d’intégration continus.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ed1f6ebda81a7f036b5e09d15ef4a27323aa9b0d
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660866"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Utiliser le package npm CI/CD Stream Analytics
Cet article explique comment utiliser le package npm CI/CD Azure Stream Analytics pour configurer un processus de déploiement et d’intégration continus.

## <a name="build-the-vs-code-project"></a>Générer le projet VS Code

Vous pouvez activer l’intégration et le déploiement continus pour les tâches Azure Stream Analytics à l’aide du package npm **asa-streamanalytics-cicd**. Le package npm fournit les outils pour générer les modèles Azure Resource Manager des projets [Stream Analytics pour Visual Studio Code](quick-create-vs-code.md). Il peut être utilisé avec Windows, macOS et Linux sans installer Visual Studio Code.

Vous pouvez [télécharger le package](https://www.npmjs.com/package/azure-streamanalytics-cicd) directement, ou l’installer [globalement](https://docs.npmjs.com/downloading-and-installing-packages-globally) via la commande `npm install -g azure-streamanalytics-cicd`. Il s’agit de l’approche recommandée, qui peut également être utilisée dans une tâche de script PowerShell ou Azure CLI d’un pipeline de build dans **Azure DevOps Pipelines**.

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
Découvrez comment [effectuer un déploiement avec un fichier de modèle Resource Manager et Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Découvrez comment [utiliser un objet en tant que paramètre dans un modèle Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Pour utiliser une identité managée pour Azure Data Lake Store Gen1 comme récepteur de sortie, vous devez fournir l’accès au principal de service à l’aide de PowerShell avant de déployer sur Azure. Découvrez plus d’informations sur le [déploiement d’ADLS Gen1 avec une identité managée et le modèle Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un travail cloud Azure Stream Analytics dans Visual Studio Code (préversion)](quick-create-vs-code.md)
* [Tester des requêtes Stream Analytics localement avec Visual Studio Code (Préversion)](visual-studio-code-local-run.md)
* [Explorer Azure Stream Analytics avec Visual Studio Code (préversion)](visual-studio-code-explore-jobs.md)
