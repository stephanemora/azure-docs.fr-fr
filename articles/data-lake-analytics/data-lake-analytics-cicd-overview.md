---
title: Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics | Microsoft Docs
description: Découvrez comment configurer l’intégration et le déploiement continus pour Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145007"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics

Dans ce document, vous allez apprendre à configurer le pipeline CI/CD pour des tâches et des bases de données U-SQL.

## <a name="cicd-for-u-sql-job"></a>CI/CD pour une tâche U-SQL

Azure Data Lake Tools pour Visual Studio fournit le type de projet U-SQL qui vous permet d’organiser des scripts U-SQL. L’utilisation du projet U-SQL pour la gestion de votre code U-SQL facilite la suite des scénarios de CI/CD.

## <a name="build-u-sql-project"></a>Générer un projet U-SQL

Le projet U-SQL peut être généré avec MSBuild en passant les paramètres correspondants. Suivez les étapes ci-dessous pour configurer le processus de génération de projets U-SQL.

### <a name="project-migration"></a>Migration du projet

Avant de configurer la tâche de génération de projet U-SQL, assurez-vous que vous utilisez bien la dernière version de ce projet. Ouvrez le fichier de projet U-SQL dans l’éditeur et vérifiez que vous avez les éléments d’importation ci-dessous :

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Si ce n’est pas le cas, vous avez deux options pour migrer le projet :

- Option 1 : transformer l’ancien élément d’importation pour en faire l’élément ci-dessus.
- Option 2 : ouvrir l’ancien projet dans une version d’Azure Data Lake Tools pour Visual Studio ultérieure à 2.3.3000.0. L’ancien modèle de projet sera automatiquement mis à niveau vers la dernière version. Le nouveau projet créé après la version 2.3.3000.0 utilise directement le nouveau modèle.

### <a name="get-nuget-package"></a>Obtenir un Package NuGet

MSBuild ne fournit pas la prise en charge intégrée de projets de type U-SQL. Pour bénéficier de cette possibilité supplémentaire, vous devez ajouter une référence à votre solution au [package Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) afin d’inclure le service de langage requis.

Pour ajouter la référence du package NuGet, cliquez avec le bouton droit sur la solution dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet**. Vous pouvez également ajouter un fichier appelé `packages.config` au dossier de la solution et y mettre les contenus ci-dessous.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gérer les références de base de données U-SQL

Si les scripts U-SQL dans le projet U-SQL contiennent des instructions de requête pour des objets de base de données U-SQL, par exemple, interroger une table U-SQL ou référencer un assembly, vous devez référencer le projet de base de données U-SQL correspondant qui inclut la définition de ces objets avant de générer ce projet U-SQL.

[En savoir plus sur le projet de base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>Le projet de base de données U-SQL est actuellement disponible en préversion publique. Si l’instruction DROP est contenue dans le projet, la build échoue. L’instruction DROP sera autorisée prochainement.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Générer un projet U-SQL avec une ligne de commande MSBuild

Après la migration du projet et l’obtention du package NuGet, vous pouvez appeler la ligne de commande MSBuild standard avec les arguments supplémentaires ci-dessous pour générer votre projet U-SQL :

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

La définition des arguments et les valeurs sont :

* USQLSDKPath = <U-SQL Nuget package>\build\runtime : ce paramètre désigne le chemin d’installation du package NuGet pour le service de langage U-SQL.
* USQLTargetType=Fusionner ou SyntaxCheck :
    * Fusionner : Le mode Fusionner compile des fichiers code-behind, tels que des fichiers .cs, .py et .r, et inclut la bibliothèque de codes définie par l’utilisateur qui en résulte (par exemple du code binaire dll, Python ou R) dans le script U-SQL.
    * SyntaxCheck : le mode SyntaxCheck fusionne tout d’abord les fichiers code-behind dans le script U-SQL, puis compile le script U-SQL pour valider votre code.
* DataRoot =<DataRoot path> : DataRoot est nécessaire uniquement pour le mode SyntaxCheck. Lors de la génération du script avec le mode SyntaxCheck, MSBuild vérifie les références aux objets de base de données dans le script. Veillez à configurer un environnement local adapté contenant les objets référencés issus de la base de données U-SQL sur le dossier DataRoot de l’ordinateur de build avant la génération. Vous pouvez également gérer ces dépendances de base de données en [référençant un projet de base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Notez que MSBuild vérifie uniquement la référence des objets de base de données, pas les fichiers.
* EnableDeployment=true ou false : EnableDeployment indique s’il est autorisé à déployer des bases de données U-SQL référencées pendant le processus de génération. Si vous référencez le projet de base de données U-SQL et que vous consommez les objets de base de données dans votre script U-SQL, définissez ce paramètre sur true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Intégration continue avec Visual Studio Team Service

En plus de la ligne de commande, les clients peuvent également utiliser un build Visual Studio ou une tâche MSBuild pour générer des projets U-SQL dans Visual Studio Team Service. Pour configurer une tâche de génération, veillez à ce qui suit :

1.  Ajoutez une tâche de restauration NuGet pour obtenir le package NuGet référencé pour la solution, notamment `Azure.DataLake.USQL.SDK`, de sorte que MSBuild puisse rechercher les cibles de langage U-SQL. Définissez **Avancé > répertoire Destination** comme `$(Build.SourcesDirectory)/packages` si vous souhaitez utiliser l’exemple d’arguments MSBuild directement à l’étape 2.

    ![Tâche de CI CD MSBuild définie par Data Lake pour un projet U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Tâche de CI CD Nuget définie par Data Lake pour un projet U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Définissez les arguments MSBuild, et vous pourrez définir les arguments dans une tâche Visual Studio Build ou MSBuild comme décrit ci-dessous, ou bien des variables pour ces arguments dans la définition de build VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Variables de CI CD MSBuild définies par Data Lake pour un projet U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Sortie de build de projet U-SQL

Après avoir exécuté la build, tous les scripts du projet U-SQL sont générés et sortis dans un fichier zip nommé `USQLProjectName.usqlpack`. La structure des dossiers dans votre projet sera conservée dans la sortie de build compressée.

>[!NOTE]
>
>Le fichier code-behind pour chaque script U-SQL sera fusionné en tant qu’instruction incluse à la sortie de build du script.
>

## <a name="test-u-sql-script"></a>Tester un script U-SQL

Azure Data Lake fournit des projets de test pour le script U-SQL et C# UDO/UDAG/UDF :
* [Découvrez comment ajouter des cas de test pour le script U-SQL et le code C# étendu](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Découvrez comment exécuter ces cas de test dans Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Déploiement d’une tâche U-SQL

Après avoir vérifié le code via le processus de build et de test, vous pouvez soumettre les travaux U-SQL directement de Visual Studio Team Service à la **tâche Azure PowerShell**. Vous pouvez également déployer le script vers le stockage Blob Azure/Azure Data Lake Store et [exécuter les tâches planifiées via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Envoyer des tâches U-SQL via Visual Studio Team Service

La sortie de build du projet U-SQL est un fichier zip nommé **USQLProjectName.usqlpack** qui inclut tous les scripts U-SQL du projet. Vous pouvez utiliser la [tâche PowserShell d’Azure dans Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) avec l’exemple de script PowserShell ci-dessous pour envoyer les tâches U-SQL directement à partir de la build de Visual Studio Team Service build ou du pipeline de mise en production.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Déployer des travaux U-SQL via Azure Data Factory

En plus d’envoyer des tâches U-SQL directement à partir de Visual Studio Team Service, vous pouvez également charger les scripts générés pour le stockage Blob Azure/Azure Data Lake Store et [exécuter les tâches planifiées via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Utilisez la tâche [Azure PowerShell dans Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) avec l’exemple de script PowerShell pour charger les scripts U-SQL sur le compte Azure Data Lake Store.

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>CI/CD pour base de données U-SQL

Azure Data Lake Tools pour Visual Studio fournit le modèle de projet de base de données U-SQL, qui permet aux développeurs de développer, gérer et déployer rapidement et facilement des bases de données U-SQL. [En savoir plus sur le projet de base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Générer un projet de base de données U-SQL

### <a name="get-nuget-package"></a>Obtenir un Package NuGet

MSBuild ne fournit pas la prise en charge intégrée du type de projet de base de données U-SQL. Pour bénéficier de cette possibilité supplémentaire, vous devez ajouter une référence à votre solution au [package Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) afin d’inclure le service de langage requis.

Pour ajouter la référence du package NuGet, cliquez avec le bouton droit sur la solution dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet** sous Solution, puis rechercher et installer le package NuGet. Vous pouvez également ajouter un fichier appelé « packages.config » au dossier de la solution et y mettre les contenus ci-dessous.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Générer un projet de base de données U-SQL avec une ligne de commande MSBuild

Vous pouvez appeler la ligne de commande MSBuild standard et transférer la référence du package NuGet du kit de développement logiciel (SDK) U-SQL en tant qu’argument supplémentaire comme ci-dessous pour générer votre projet de base de données U-SQL :

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Les arguments `USQLSDKPath=<U-SQL Nuget package>\build\runtime` font référence au chemin d’installation du package NuGet pour le service de langage U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Intégration continue avec Visual Studio Team Service

En plus de la ligne de commande, les clients peuvent également utiliser **Visual Studio Build** ou une **tâche MSBuild** pour générer des projets de base de données U-SQL dans Visual Studio Team Service. Pour configurer une tâche de génération, veillez à ce qui suit :

1.  Ajoutez une tâche de restauration NuGet pour obtenir le package NuGet référencé pour la solution, notamment `Azure.DataLake.USQL.SDK`, de sorte que MSBuild puisse rechercher les cibles de langage U-SQL. Définissez **Avancé > répertoire Destination** comme `$(Build.SourcesDirectory)/packages` si vous souhaitez utiliser l’exemple d’arguments MSBuild directement à l’étape 2.

    ![Tâche de CI CD MSBuild définie par Data Lake pour un projet U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Tâche de CI CD Nuget définie par Data Lake pour un projet U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Définissez les arguments MSBuild, et vous pourrez définir les arguments dans une tâche Visual Studio Build ou MSBuild comme décrit ci-dessous, ou bien des variables pour ces arguments dans la définition de build VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Variables de CI CD MSBuild définie par Data Lake pour un projet de base de données U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Sortie de build de projet de base de données U-SQL

La sortie de build du projet de base de données U-SQL est un package de déploiement de base de données U-SQL dont le nom est suivi du suffixe `.usqldbpack`. Le package `.usqldbpack` est un fichier zip incluant toutes les instructions DDL dans un seul script U-SQL du dossier DDL, ainsi que tous les fichiers dll et supplémentaires dans le dossier Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Fonction table de test et procédure stockée

L’ajout direct de cas de test pour les fonctions table et les procédures stockées n’est actuellement pas pris en charge. Pour contourner ce problème, vous pouvez créer un projet U-SQL contenant des scripts U-SQL qui appellent ces fonctions et écrire des cas de test pour elles. Suivez les étapes ci-dessous afin de configurer des cas de test pour les fonctions table et les procédures stockées définies dans le projet de base de données U-SQL :

1.  Créez un projet U-SQL à des fins de test et écrivez des scripts U-SQL qui appellent les fonctions de table et les procédures stockées.
2.  Ajoutez une référence de base de données à ce projet U-SQL. Pour obtenir la fonction table et la définition de la procédure stockée, vous devez référencer le projet de base de données qui contient l’instruction DDL. [En savoir plus sur la référence de base de données](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Ajouter des cas de test pour les scripts U-SQL qui appellent des fonctions table et des procédures stockées. [Découvrez comment ajouter des cas de test pour le script U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Déployer une base de données U-SQL via Visual Studio Team Service

`PackageDeploymentTool.exe` fournit les interfaces de programmation et de ligne de commande qui vous aident à déployer le package de base de données U-SQL (.usqldbpack). Le kit de développement logiciel (SDK) est inclus dans le [package NuGet du kit de développement logiciel (SDK) U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), situé à l’emplacement build/runtime/PackageDeploymentTool.exe. À l’aide de `PackageDeploymentTool.exe`, vous pouvez déployer des bases de données U-SQL sur Azure Data Lake Analytics et sur le compte local.

>[!NOTE]
>
>La prise en charge de la ligne de commande PowerShell et de la tâche de mise en production de Visual Studio Team Service pour le déploiement d’une base de données U-SQL est en cours de réalisation.
>

Suivez les étapes ci-dessous pour configurer la tâche de déploiement d’une base de données dans Visual Studio Team Service :

1. Ajoutez une tâche de script PowerShell au pipeline de build ou de mise en production et exécutez le script PowerShell ci-dessous. Cette tâche permet d’obtenir des dépendances du kit SDK Azure pour `PackageDeploymentTool.exe` et `PackageDeploymentTool.exe`. Vous pouvez définir les paramètres -AzureSDK et -DBDeploymentTool pour charger les dépendances et l’outil de déploiement dans différents dossiers spécifiques. Passez le chemin -AzureSDK à `PackageDeploymentTool.exe` en tant que paramètre -AzureSDKPath à l’étape 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Ajoutez une **tâche de ligne de commande** au pipeline de build ou de mise en production et remplissez le script appelant `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` se trouve sous le dossier $DBDeploymentTool défini. L’exemple de script U-SQL est le suivant : 

    * Déployer localement une base de données U-SQL

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Utilisez le mode d’authentification interactive pour déployer la base de données U-SQL sur un compte Azure Data Lake Analytics :

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Utilisez l’authentification secrète pour déployer la base de données U-SQL sur un compte Azure Data Lake Analytics :

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Utilisez l’authentification certFile pour déployer la base de données U-SQL sur un compte Azure Data Lake Analytics :

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Description du paramètre PackageDeploymentTool.exe :**

**Paramètres communs :**

|Paramètre|Description|Valeur par défaut|Obligatoire|
|---------|-----------|-------------|--------|
|Package|Chemin d’accès du package de déploiement de base de données U-SQL à déployer|null|true|
|Base de données|Nom de la base données à créer/vers laquelle le déploiement doit être effectué|master|false|
|LogFile|Chemin d'accès du fichier pour la journalisation, sortie par défaut à standard (console)|null|false|
|LogLevel|Niveau de journal : détaillé, normal, avertissement, erreur|LogLevel.Normal|false|

**Paramètre pour le déploiement local :**

|Paramètre|Description|Valeur par défaut|Obligatoire|
|---------|-----------|-------------|--------|
|DataRoot|Chemin d'accès du dossier racine des données local|null|true|

**Paramètre pour le déploiement d’Azure Data Lake Analytics :**

|Paramètre|Description|Valeur par défaut|Obligatoire|
|---------|-----------|-------------|--------|
|Compte|Désigne par son nom le compte Azure Data Lake Analytics vers lequel le déploiement sera effectué|null|true|
|ResourceGroup|Nom du groupe de ressources Azure pour le compte Azure Data Lake Analytics|null|true|
|SubscriptionId|ID d’abonnement Azure pour le compte Azure Data Lake Analytics|null|true|
|Locataire|Nom du locataire (nom de domaine du répertoire AAD, que vous trouverez cela dans la page de gestion d’abonnement dans le portail Azure)|null|true|
|AzureSDKPath|Chemin d’accès pour rechercher des assemblys dépendants dans le kit de développement logiciel (SDK) Azure|null|true|
|Interactive|Utiliser ou non le mode interactif pour l’authentification|false|false|
|ClientId|ID d’application AAD pour aucune authentification interactive, requis pour aucune authentification interactive|null|requis pour aucune authentification interactive|
|Authentification secrète|Authentification secrète/mot de passe pour aucune authentification interactive, seul un environnement approuvé/sécurisé doit être utilisé|null|requis pour aucune authentification interactive, ou utiliser SecreteFile|
|SecreteFile|Le fichier enregistre l’authentification secrète/le mot de passe pour aucune authentification interactive, veillez à ce que cette authentification secrète/ce mot de passe ne soient visibles que pour l’utilisateur actuel|null|requis pour aucune authentification interactive, ou utiliser l’authentification secrète|
|CertFile|Le fichier enregistre la certification X.509 pour aucune authentification interactive, par défaut, l’authentification secrète du client est utilisée|null|false|
|JobPrefix|Préfixe pour la tâche de déploiement de la base de données U-SQL DDL|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>Étapes suivantes

- [Comment tester votre code Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md)
- [Utiliser un projet de base de données U-SQL afin de développer une base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
