---
title: Annotations de version pour Application Insights | Microsoft Docs
description: Ajouter des marqueurs déploiement ou de build aux graphiques Metrics Explorer dans Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476170"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotations sur les graphiques de métriques dans Application Insights

Des annotations sur les graphiques [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) montrent les endroits où vous avez déployé une nouvelle build ou tout autre événement majeur. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le [système de génération Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Vous pouvez également créer des annotations pour tout événement en les créant à partir de PowerShell.

> [!NOTE]
> Cet article reflète **l’expérience de métriques classique** qui est obsolète. Les annotations ne sont actuellement disponibles que dans l’expérience classique et dans les **[classeurs](../../azure-monitor/app/usage-workbooks.md)** . Pour en savoir plus sur l’expérience actuelle des métriques, vous pouvez consulter [cet article](../../azure-monitor/platform/metrics-charts.md).

![Exemples d’annotations](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Annotations de version avec la build Azure DevOps Services

Annotations de version est une fonctionnalité du service Azure Pipelines basé sur le cloud Azure Pipelines d’Azure DevOps Services.

### <a name="install-the-annotations-extension-one-time"></a>Installer l’extension Annotations (une fois)
Pour pouvoir créer des annotations de version, vous devez installer l’une des nombreuses extensions Azure DevOps Services disponibles dans Visual Studio Marketplace.

1. Connectez-vous à votre [Azure DevOps Services](https://azure.microsoft.com/services/devops/) projet.
2. Dans Visual Studio Marketplace, [obtenez l’extension Annotations de version](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)et ajoutez-la à votre organisation Azure DevOps Services.

![Sélectionnez une organisation Azure DevOps et procédez à l’installation.](./media/annotations/1-install.png)

Il vous suffit de le faire une seule fois pour votre organisation Azure DevOps Services. Des annotations de version peuvent désormais être configurées pour n’importe quel projet de votre organisation.

### <a name="configure-release-annotations"></a>Configurer des annotations de version

Vous devez obtenir une clé API distincte pour chaque modèle de version Azure DevOps Services.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui surveille votre application. (Ou [créez-en une maintenant](../../azure-monitor/app/app-insights-overview.md)si vous ne l’avez pas encore fait.)
2. Ouvrez l’onglet **Accès à l’API** et copiez l’**ID d’Application Insights**.
   
    ![Dans portal.azure.com, ouvrez votre ressource Application Insights, puis choisissez Paramètres. Ouvrir Accès API. Copier l’ID de l’application](./media/annotations/2-app-id.png)

4. Dans une fenêtre de navigateur distincte, ouvrez (ou créez) le modèle de version qui gère vos déploiements à partir d’Azure DevOps Services.
   
    Ajoutez une tâche, sélectionnez la tâche d’annotation de version d’Application Insights à partir du menu.

   ![Cliquez sur le signe plus pour ajouter une tâche, puis sélectionnez la tâche d’annotation de version d’Application Insights. Collez l’ID Application Insights.](./media/annotations/3-add-task.png)

    Collez **l’ID de l’application** que vous avez copié à partir de l’onglet Accès API.
   
    ![Coller l’ID Application Insights](./media/annotations/4-paste-app-id.png)

5. De retour dans la fenêtre Azure, créez une clé API et copiez-la.
   
    ![Dans l’onglet Accès API de la fenêtre Azure, cliquez sur Créer une clé API.](./media/annotations/5-create-api-key.png)

    ![Fournissez un commentaire dans l’onglet Créer une clé API, cochez Écrire des annotations, puis cliquez sur Générer une clé. Copiez la nouvelle clé.](./media/annotations/6-create-api-key.png)

6. Ouvrez l’onglet Configuration du modèle de version.
   
    Créez une définition de variable pour `ApiKey`.
   
    Collez votre clé API pour la définition de la variable APIKey.
   
    ![Dans la fenêtre Azure DevOps Services, sélectionnez l’onglet Variable, puis cliquez sur Ajouter. Définissez le nom ApiKey et, dans Valeur, collez la clé que vous avez générée, puis cliquez sur l’icône de verrouillage.](./media/annotations/7-paste-api-key.png)
1. Enfin, **enregistrez** le pipeline de mise en production.


## <a name="view-annotations"></a>Afficher les annotations
Désormais, lorsque vous utilisez le modèle de version pour déployer une nouvelle version, une annotation est envoyée à Application Insights. Les annotations figureront sur les graphiques Metrics Explorer.

Cliquez sur un marqueur d’annotation (flèche gris clair) pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, le pipeline de mise en production, l’environnement et bien plus encore.

![Cliquez sur un marqueur d’annotation de version.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Créer des annotations personnalisées à partir de PowerShell
Vous pouvez également créer des annotations à partir du processus de votre choix (sans utiliser Azure DevOps Services). 


1. Créez une copie locale du [script Powershell à partir de GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenez l’ID d’application et créez une clé API à partir de l’onglet Accès d’API.

3. Appelez le script comme suit :

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Il est facile de modifier le script, par exemple pour créer des annotations pour les événements passés.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des éléments de travail](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automation avec PowerShell](../../azure-monitor/app/powershell.md)
