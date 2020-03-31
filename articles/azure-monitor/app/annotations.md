---
title: Annotations de version pour Application Insights | Microsoft Docs
description: Ajouter des marqueurs déploiement ou de build aux graphiques Metrics Explorer dans Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666511"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotations sur les graphiques de métriques dans Application Insights

Des annotations sur les graphiques [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) indiquent où vous avez déployé une nouvelle build ou d’autres événements importants. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le système de génération [Azure DevOps Services.Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Vous pouvez également créer des annotations pour tout événement en les créant à partir de PowerShell.

> [!NOTE]
> Cet article reflète **l’expérience de métriques classique** qui est obsolète. Les annotations ne sont actuellement disponibles que dans l’expérience classique et dans les **[classeurs](../../azure-monitor/app/usage-workbooks.md)** . Pour en savoir plus sur l’interface des métriques, voir [Fonctionnalités avancées d’Azure Metrics Explorer.](../../azure-monitor/platform/metrics-charts.md)

![Exemples d’annotations](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Annotations de mise en production avec la build Azure Pipelines

Les annotations de mise en production sont une fonctionnalité du service Azure Pipelines basé sur le cloud d’Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installer l’extension Annotations (une fois)
Pour pouvoir créer des annotations de mise en production, vous devez installer l’une des nombreuses extensions Azure DevOps disponibles dans Visual Studio Marketplace.

1. Connectez-vous à votre projet [Azure DevOps](https://azure.microsoft.com/services/devops/).
   
1. Sur la page [Extension des annotations de mise en production](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) de Visual Studio Marketplace, sélectionnez votre organisation Azure DevOps, puis choisissez **Installer** pour ajouter l’extension à votre organisation Azure DevOps.
   
   ![Sélectionnez une organisation Azure DevOps, puis choisissez Installer.](./media/annotations/1-install.png)
   
Vous ne devez installer l’extension qu’une seule fois pour votre organisation Azure DevOps. Vous pouvez désormais configurer des annotations de mise en production pour tout projet au sein de votre organisation.

### <a name="configure-release-annotations"></a>Configurer des annotations de version

Créez une clé API distincte pour chacun de vos modèles de mise en production Azure Pipelines.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui surveille votre application. Si vous n’en avez pas, [créez une ressource Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Ouvrez l’onglet **Accès à l’API** et copiez l’**ID d’Application Insights**.
   
   ![Sous Accès d’API, copiez l’ID d’application.](./media/annotations/2-app-id.png)

1. Dans une fenêtre de navigateur distincte, ouvrez ou créez le modèle de mise en production qui gère vos déploiements Azure Pipelines.
   
1. Sélectionnez **Ajouter une tâche**, puis choisissez la tâche **Annotation de mise en production Application Insights** dans le menu.
   
   ![Sélectionnez Ajouter une tâche, puis choisissez la tâche Annotation de mise en production Application Insights.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Actuellement, la tâche d’annotation de mise en production ne prend en charge que les agents Windows. Elle ne s’exécute pas sur Linux, macOS ou d’autres types d’agents.
   
1. Sous **ID d’application**, collez l’ID Application Insights que vous avez copié à partir de l’onglet **Accès d’API**.
   
   ![Coller l’ID Application Insights](./media/annotations/4-paste-app-id.png)
   
1. De retour dans la fenêtre **Accès d’API**  d’Application Insights, sélectionnez **Créer une clé API**. 
   
   ![Sous l’onglet Accès d’API, sélectionnez Créer une clé API.](./media/annotations/5-create-api-key.png)
   
1. Dans la fenêtre **Créer une clé API**, entrez une description, sélectionnez **Écrire des annotations**, puis sélectionnez **Générer une clé**. Copiez la nouvelle clé.
   
   ![Dans la fenêtre Créer une clé API, entrez une description, sélectionnez Écrire des annotations, puis sélectionnez Générer une clé.](./media/annotations/6-create-api-key.png)
   
1. Dans la fenêtre du modèle de mise en production, sous l’onglet **Variables**, sélectionnez **Ajouter** pour créer une définition de variable pour la nouvelle clé API.

1. Sous **Nom**, entrez `ApiKey`, et sous **Valeur**, collez la clé API que vous avez copiée à partir de l’onglet **Accès d’API**.
   
   ![Dans l’onglet Variables Azure DevOps, sélectionnez Ajouter, nommez la variable ApiKey, puis collez la clé API sous Valeur.](./media/annotations/7-paste-api-key.png)
   
1. Sélectionnez **Enregistrer** dans la fenêtre principale du modèle de mise en production pour enregistrer le modèle.

## <a name="view-annotations"></a>Afficher les annotations
Désormais, lorsque vous utilisez le modèle de mise en production pour déployer une nouvelle publication, une annotation est envoyée à Application Insights. Les annotations apparaissent sur les graphiques dans **Metrics Explorer**.

Sélectionnez un marqueur d’annotation (flèche gris clair) pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, le pipeline de mise en production et l’environnement.

![Sélectionnez un marqueur d’annotation de version.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Créer des annotations personnalisées à partir de PowerShell
Vous pouvez utiliser le script PowerShell [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) de GitHub pour créer des annotations à partir de tout processus de votre choix, sans utiliser Azure DevOps. 

1. Effectuez une copie locale du fichier [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Suivez les étapes de la procédure précédente pour obtenir votre ID Application Insights et créer une clé API à partir de l’onglet **Accès d’API** d’Application Insights.
   
1. Appelez le script PowerShell avec le code suivant, en remplaçant les espaces réservés entre crochets angulaires par vos valeurs. Les `-releaseProperties` sont facultatives. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Vous pouvez modifier le script, par exemple, pour créer des annotations pour le passé.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des éléments de travail](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automation avec PowerShell](../../azure-monitor/app/powershell.md)
