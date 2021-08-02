---
title: Annotations de version pour Application Insights | Microsoft Docs
description: Découvrez comment créer des annotations pour suivre le déploiement ou d’autres événements importants avec Application Insights.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: cfd1c9b28a79d68983e49ef5d6dfd70dd357ab47
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071026"
---
# <a name="release-annotations-for-application-insights"></a>Annotations de version pour Application Insights

Des annotations indiquent où vous avez déployé une nouvelle build, ou d’autres événements importants. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le système de génération [Azure DevOps Services.Azure Pipelines](/azure/devops/pipelines/tasks/). Vous pouvez également créer des annotations pour tout événement en les créant à partir de PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Annotations de mise en production avec la build Azure Pipelines

Les annotations de mise en production sont une fonctionnalité du service Azure Pipelines basé sur le cloud d’Azure DevOps.

Si tous les critères suivants sont remplis, la tâche de déploiement crée automatiquement l’annotation de version :

- La ressource sur laquelle vous effectuez le déploiement est liée à Application Insights (via le paramètre d’application `APPINSIGHTS_INSTRUMENTATIONKEY`).
- La ressource Application Insights se trouve dans le même abonnement que la ressource sur laquelle vous effectuez le déploiement.
- Vous utilisez l’une des tâches de pipeline Azure DevOps suivantes :

    | Code de tâche                 | Nom de la tâche                     | Versions     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Paramètres d’Azure App Service    | Quelconque          |
    | AzureRmWebAppDeployment   | Déploiement Azure App Service      | V3 et ultérieures |
    | AzureFunctionApp          | Azure Functions               | Quelconque          |
    | AzureFunctionAppContainer | Azure Functions pour conteneur | Quelconque          |
    | AzureWebAppContainer      | Azure Web App pour conteneurs  | Quelconque          |
    | AzureWebApp               | Application web Azure                 | Quelconque          |

> [!NOTE]
> Si vous utilisez toujours la tâche de déploiement d’annotations Application Insights, vous devez la supprimer.

### <a name="configure-release-annotations"></a>Configurer des annotations de version

Si vous ne pouvez pas utiliser l’une des tâches de déploiement de la section précédente, vous devez ajouter une tâche de script inlined dans votre pipeline de déploiement.

1. Accédez à un pipeline nouveau ou existant, puis sélectionnez une tâche.
    :::image type="content" source="./media/annotations/task.png" alt-text="Capture d’écran de la tâche dans les étapes sélectionnées." lightbox="./media/annotations/task.png":::
1. Ajoutez une nouvelle tâche, puis sélectionnez **Azure CLI**.
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="Capture d’écran montrant l’ajout d’une nouvelle tâche et la sélection d’Azure CLI." lightbox="./media/annotations/add-azure-cli.png":::
1. Spécifiez l’abonnement Azure correspondant.  Définissez **Type de script** sur *PowerShell* et **Emplacement du script** sur *Inlined*.
1. Ajoutez le [script PowerShell de l’étape 2 de la section suivante](#create-release-annotations-with-azure-cli) dans la zone **Script inlined**.
1. Ajoutez les arguments ci-dessous, en remplaçant les espaces réservés entre crochets par vos valeurs dans **Arguments de script**. Les paramètres -releaseProperties sont facultatifs.

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="Capture d’écran des paramètres de tâche Azure CLI avec les paramètres Type de script, Emplacement du script, Script inlined et Arguments de script mis en évidence." lightbox="./media/annotations/inline-script.png":::

1. Enregistrez.

## <a name="create-release-annotations-with-azure-cli"></a>Créer des annotations de version avec Azure CLI

Vous pouvez utiliser le script PowerShell CreateReleaseAnnotation pour créer des annotations à partir de tout processus de votre choix sans utiliser Azure DevOps.

1. Connectez-vous à [Azure CLI](/cli/azure/authenticate-azure-cli).

2. Faites une copie locale du script ci-dessous et appelez-le CreateReleaseAnnotation.ps1.

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""

    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "
    ```

3. Appelez le script PowerShell avec le code suivant, en remplaçant les espaces réservés entre crochets angulaires par vos valeurs. Les paramètres -releaseProperties sont facultatifs.

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|Argument | Définition | Notes|
|--------------|-----------------------|--------------------|
|aiResourceId | ID de ressource de la ressource Application Insights cible. | Exemple :<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | Nom à donner à l’annotation de version créée. | | 
|releaseProperties | Utilisé pour joindre des métadonnées personnalisées à l’annotation. | Facultatif|

## <a name="view-annotations"></a>Afficher les annotations

> [!NOTE]
> Les annotations de version ne sont actuellement pas disponibles dans le volet Métriques d’Application Insights

Désormais, lorsque vous utilisez le modèle de mise en production pour déployer une nouvelle publication, une annotation est envoyée à Application Insights. Les annotations peuvent être affichées aux emplacements suivants :

- Performances

    :::image type="content" source="./media/annotations/performance.png" alt-text="Capture d’écran de l’onglet Performances avec une annotation de version (flèche bleue) sélectionnée pour afficher l’onglet Propriétés de la version." lightbox="./media/annotations/performance.png":::

- Échecs

    :::image type="content" source="./media/annotations/failures.png" alt-text="Capture d’écran de l’onglet Échecs avec une annotation de version (flèche bleue) sélectionnée pour afficher l’onglet Propriétés de la version." lightbox="./media/annotations/failures.png":::
- Utilisation

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="Capture d’écran de la barre d’onglets Utilisateurs avec les annotations de version sélectionnées. Les annotations de version apparaissent sous forme de flèches bleues au-dessus du graphique, indiquant le moment où une mise en production a eu lieu." lightbox="./media/annotations/usage-pane.png":::

- Workbooks

    Dans toute requête de classeur basée sur un journal, où la visualisation affiche l’heure le long de l’axe x.
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="Capture d’écran du volet Classeurs avec une requête basée sur le journal de série chronologique et des annotations affichées." lightbox="./media/annotations/workbooks-annotations.png":::
    
    Pour activer les annotations dans votre classeur, accédez à **Paramètres avancés** et sélectionnez **Afficher les annotations**.
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="Capture d’écran du menu Paramètres avancés avec la case à cocher Afficher les annotations en évidence.":::

Sélectionnez un marqueur d’annotation pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, le pipeline de mise en production et l’environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des éléments de travail](./diagnostic-search.md#create-work-item)
* [Automation avec PowerShell](./powershell.md)
