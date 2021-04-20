---
title: Annotations de version pour Application Insights | Microsoft Docs
description: Ajouter des marqueurs déploiement ou de build aux graphiques Metrics Explorer dans Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 9132e65e4705fd9125d97a5e095fe5f0850229a2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011048"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotations sur les graphiques de métriques dans Application Insights

Des annotations indiquent où vous avez déployé une nouvelle build, ou d’autres événements importants. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le système de génération [Azure DevOps Services.Azure Pipelines](/azure/devops/pipelines/tasks/). Vous pouvez également créer des annotations pour tout événement en les créant à partir de PowerShell.

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

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui surveille votre application. Si vous n’en avez pas, [créez une ressource Application Insights](./app-insights-overview.md).
   
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


   > [!NOTE]
   > Les limites pour les clés API sont décrites dans la [documentation relative aux limites de taux de l’API REST](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Afficher les annotations


   > [!NOTE]
   > Les annotations de version ne sont actuellement pas disponibles dans le volet Métriques d’Application Insights

Désormais, lorsque vous utilisez le modèle de mise en production pour déployer une nouvelle publication, une annotation est envoyée à Application Insights. Les annotations peuvent être affichées aux emplacements suivants :

Volet **Utilisation**, où vous pouvez également créer manuellement des annotations de version :

![Capture d’écran du graphique à barres, avec le nombre de visites d’utilisateurs affiché sur une période de quelques heures. Les annotations de version s’affichent sous la forme de coches vertes au-dessus du graphique et indiquent quand une mise en production a eu lieu.](./media/annotations/usage-pane.png)

Dans toute requête de classeur basée sur un journal, où la visualisation affiche l’heure le long de l’axe x.

![Capture d’écran du volet Classeurs avec requête basée sur le journal de la série chronologique et les annotations affichées](./media/annotations/workbooks-annotations.png)

Pour activer les annotations dans votre classeur, accédez à **Paramètres avancés** et sélectionnez **Afficher les annotations**.

![Capture d’écran du menu Paramètres avancés avec les mots Afficher les annotations en surbrillance avec une coche en regard du paramètre pour l’activer.](./media/annotations/workbook-show-annotations.png)

Sélectionnez un marqueur d’annotation pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, le pipeline de mise en production et l’environnement.

## <a name="create-custom-annotations-from-powershell"></a>Créer des annotations personnalisées à partir de PowerShell
Vous pouvez utiliser le script PowerShell CreateReleaseAnnotation pour créer des annotations à partir de tout processus de votre choix sans utiliser Azure DevOps.

> [!IMPORTANT]
> Si vous utilisez PowerShell 7.1, ajoutez `-SkipHttpErrorCheck` à la fin de la ligne 26. Par exemple : `$request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore -SkipHttpErrorCheck`.

1. Effectuez une copie locale du fichier CreateReleaseAnnotation.ps1 :

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
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

* [Créer des éléments de travail](./diagnostic-search.md#create-work-item)
* [Automation avec PowerShell](./powershell.md)
